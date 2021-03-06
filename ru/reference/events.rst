Менеджер событий EventsManager
==============================
Цель данного компонента состоит в добавлени возможности перехватывать процесс выполнения большинства компонентов системы путём создания
специальных "ключевых точек". Эти ключевые точки позволяют разработчику получить информацию о состоянии, манипулировать данными и изменять 
процесс работы компонента.

Пример использования
--------------------
В следующем примере, мы используем менеджер событий для прослушивания событий вызываемых в MySQL соединении управляемым :doc:`Phalcon\\Db <../api/Phalcon_Db>`.
Для начала нам необходимо создать объект слушателея. Методы класса являются событиями, которые необходимо прослушивать.

.. code-block:: php

    <?php

    class MyDbListener
    {

        public function afterConnect()
        {

        }

        public function beforeQuery()
        {

        }

        public function afterQuery()
        {

        }

    }

Такой класс может реализовывать необходимые нам события. Менеджер событий будет взаимодействовать между компонентом и нашим классом,
вызывая события, реализованне методами класса и поддерживаемые компонентом.

.. code-block:: php

    <?php

    $eventsManager = new \Phalcon\Events\Manager();

    // Создание слушателе базы данных
    $dbListener = new MyDbListener()

    // Слушать все события базы данных
    $eventsManager->attach('db', $dbListener);

    $connection = new \Phalcon\Db\Adapter\Pdo\Mysql(array(
        "host" => "localhost",
        "username" => "root",
        "password" => "secret",
        "dbname" => "invo"
    ));

    // Совмещение менеджера событий с адаптером базы данных
    $connection->setEventsManager($eventsManager);

    // Выполнение SQL запроса
    $connection->query("SELECT * FROM products p WHERE p.status = 1");

In order to log all the SQL statements executed by our application, we need to use the event “afterQuery”. The first parameter passed to
the event listener contains contextual information about the event that is running, the second is the connection itself.

Для того, чтобы получать все SQL-запросы, выполненные в нашем приложении, мы должны использовать событие “afterQuery”. Первый передаваемый слушателю параметр
содержит контекстную информацию о текущем событии, второй параметр - само соединение.

.. code-block:: php

    <?php

    class MyDbListener
    {

        protected $_logger;

        public function __construct()
        {
            $this->_logger = new \Phalcon\Logger\Adapter\File("../apps/logs/db.log");
        }

        public function afterQuery($event, $connection)
        {
            $this->_logger->log($connection->getSQLStatement(), \Phalcon\Logger::INFO);
        }

    }

В рамках этого примера, мы будем также использовать профайлер Phalcon\\Db\\Profiler для обнаружения SQL-запросов с длительным временем выполнения:

.. code-block:: php

    <?php

    class MyDbListener
    {

        protected $_profiler;

        protected $_logger;

        public function __construct()
        {
            $this->_profiler = new \Phalcon\Db\Profiler();
            $this->_logger = new \Phalcon\Logger\Adapter\File("../apps/logs/db.log");
        }

        public function beforeQuery($event, $connection)
        {
            $this->_profiler->startProfile($connection->getSQLStatement());
        }

        public function afterQuery($event, $connection)
        {
            $this->_logger->log($connection->getSQLStatement(), \Phalcon\Logger::INFO);
            $this->_profiler->stopProfile();
        }

        public function getProfiler()
        {
            return $this->_profiler;
        }

    }

Результирующие данные о работе профайлера могут быть получены из слушателя:

.. code-block:: php

    <?php

    // Выполнение SQL запроса
    $connection->execute("SELECT * FROM products p WHERE p.status = 1");

    foreach($dbListener->getProfiler()->getProfiles() as $profile){
        echo "SQL Statement: ", $profile->getSQLStatement(), "\n";
        echo "Start Time: ", $profile->getInitialTime(), "\n"
        echo "Final Time: ", $profile->getFinalTime(), "\n";
        echo "Total Elapsed Time: ", $profile->getTotalElapsedSeconds(), "\n";
    }

Подобным образом мы можем зарегистрировать лямбда-функцию для выполнения этой задачи, без использования отдельного класса слушателя (как в примере выше):

.. code-block:: php

    <?php

    // Слушаем все события базы данных
    $eventManager->attach('db', function($event, $connection) {
        if ($event->getType() == 'afterQuery') {
            echo $connection->getSQLStatement();
        }
    });

Создание компонентов с поддержкой событий
-----------------------------------------
Компоненты, созданные в вашем приложении могут инициироварь события в EventsManager. Вы также можете создавать слушателей, которые
реагируют на эти события. В следующем примере мы создаем компонент, под названием "MyComponent".
Этот компонент будет указывать менеджеру событий о выполнении своего метода "someTask", что в свою очередь будет вызывать два события для слушателей в EventsManager:

.. code-block:: php

    <?php

    class MyComponent implements \Phalcon\Events\EventsAwareInterface
    {

        protected $_eventsManager;

        public function setEventsManager($eventsManager)
        {
            $this->_eventsManager = $eventsManager;
        }

        public function getEventsManager()
        {
            return $this->_eventsManager
        }

        public function someTask()
        {
            $this->_eventsManager->fire("my-component:beforeSomeTask", $this);

            // тут выпполнение каких-либо действий

            $this->_eventsManager->fire("my-component:afterSomeTask", $this);
        }

    }

Note that events produced by this component are prefixed with "my-component". This is a unique word that helps us
identify events that are generated from certain component. You can even generate events outside the component with
the same name. Now let's create a listener to this component:

Обратите внимание, что события, создаваемые нашим компонентом имеют префикс "my-component". Это уникальное слово для разделения событий,
которые формируются из разных компонентах. Вы можете создавать события вне компонента с таким же именем, оно ни от чего не зависит.
Теперь давайте создадим слушателя до нашего компонента:

.. code-block:: php

    <?php

    class SomeListener
    {

        public function beforeSomeTask($event, $myComponent)
        {
            echo "Выполняется beforeSomeTask\n";
        }

        public function afterSomeTask($event, $myComponent)
        {
            echo "Выполняется afterSomeTask\n";
        }

    }

Слушатель - это просто класс, который реализует все события, вызываемые в компоненте. Давайте заставим их работать вместе:

.. code-block:: php

    <?php

    // Создаём менеджер событий
    $eventsManager = new Phalcon\Events\Manager();

    // Создаём экземпляр MyComponent
    $myComponent = new MyComponent();

    // Связываем компонент и менеджер событий
    $myComponent->setEventsManager($myComponent);

    // Связываем слушателя и менеджер событий
    $eventsManager->attach('my-component', new SomeListener());

    // Выполняем метод нашего компонента
    $myComponent->someTask();

Когда метод "someTask" выполнится, сработают оба метода слушателя, и выведутся следующие строки:

.. code-block:: php

    Выполняется beforeSomeTask
    Выполняется afterSomeTask

Во время наступления события в слушателей можно передавать дополнительные данные, они должны передаваться третим параметром в метод "fire":

.. code-block:: php

    <?php

    $eventsManager->fire("my-component:afterSomeTask", $this, $extraData);

Слушатель также получает эти данные третим параметром:

.. code-block:: php

    <?php

    // Получение данных из третьего параметра
    $eventManager->attach('my-component', function($event, $component, $data) {
        print_r($data);
    });

    // Получение данных из контекста события
    $eventManager->attach('my-component', function($event, $component) {
        print_r($event->getData());
    });

Если слушать необходимо только определённое событие, вы можете указать его в момент связывания:

.. code-block:: php

    <?php

    // Обработчик выполнится только при наступлении события "beforeSomeTask"
    $eventManager->attach('my-component:beforeSomeTask', function($event, $component) {
        //...
    });

Остановка/Продолжение событий
-----------------------------
Несколько слушатетей может быть привязано к одному событию, это означает, что при его наступлении эти слушатели будут уведомлены.
Слушатели уведомляются в порядке, в котором они были зарегистрированы в менеджере событий EventsManager. Некоторые события могут быть прекращены
во время работы слушателя и уведомление других слушателей будет остановлено.

.. code-block:: php

    <?php

    $eventsManager->attach('db', function($event, $connection){

        // Если событие поддерживает прекращение
        if ($event->isCancelable()) {
            // Прекращение события, остальные слушатели его не получат
            $event->stop();
        }

        //...

    });

По умолчанию все события поддерживают прекращение, большинство событий, выполняемых в ядре фреймворка, тоже поддерживают прекращение. Вы можете
указать что событие не прекращаемое передавая "false" в четвертый параметр вызова fire:

.. code-block:: php

    <?php

    $eventsManager->fire("my-component:afterSomeTask", $this, $extraData, false);

Настройка слушателей (Listener)
-------------------------------
При установке слушателей можно устанавливать их приоритет. Это позволяет указать порядок их вызова в момент выполнения.

.. code-block:: php

    <?php

    $evManager->attach('db', new DbListener(), 150); // Высокий приоритет
    $evManager->attach('db', new DbListener(), 100); // Нормальный приоитет
    $evManager->attach('db', new DbListener(), 50); // Низкий приоритет


Создание собственных менеджеров событий (EventsManager)
-------------------------------------------------------
Для создания менеджера необходимо реализовать интерфейс :doc:`Phalcon\\Events\\ManagerInterface <../api/Phalcon_Events_ManagerInterface>` и
заменить им стандартный менеджер EventsManager при инициализации Phalcon.
