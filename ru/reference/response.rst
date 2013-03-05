Заголовки ответа (Responses)
============================

Одной из частей работы HTTP протокола является возвращение ответа клиенту. В Phalcon существует компонент :doc:`Phalcon\\HTTP\\Response <../api/Phalcon_Http_Response>` 
для реализации этой задачи. HTTP ответ состоит из заголовков и тела ответа. Типичное ипользование Response выглядит следующим образом:

.. code-block:: php

    <?php

    // Получение экземпляра Response
    $response = new \Phalcon\Http\Response();

    // Установка кода статуса
    $response->setRawHeader(404, "Not Found");

    // Установка содержимого ответа
    $response->setContent("Сожалеем, но страница не существует");

    // Отправка ответа клиенту
    $response->send();

Имейте в виду, что при использовании полного стек MVC нет необходимости отправлять результаты Response вручную. Однако, если есть необходимость указать ответ самостоятельно
в действии контроллера, то можно использовать такой пример:

.. code-block:: php

    <?php

    class FeedController extends Phalcon\Mvc\Controller
    {

        public function getAction()
        {
            // Получение экземпляра Response
            $response = new \Phalcon\Http\Response();

            $feed = //.. тут данные

            // Установка содержимого ответа
            $response->setContent($feed->asString());

            // Возврат Response ответа
            return $response;
        }

    }

Работа с заголовками
--------------------
Заголовки являются важной часть для HTTP ответов. Он содержит полезную информацию о статусе ответа, его типе и еще многое другое.

Указывать заголовки можно следующим образом:

.. code-block:: php

    <?php

    // Установка по имени
    $response->setHeader("Content-Type", "application/pdf");
    $response->setHeader("Content-Disposition", 'attachment; filename="downloaded.pdf"');

    // Установка напрямую
    $response->setRawHeader("HTTP/1.1 200 OK");

Объект :doc:`Phalcon\\HTTP\\Response\\Headers <../api/Phalcon_Http_Response_Headers>` содержит в себе все заголовки и средства для их управления.
Этот класс позволяет управлять заголовками до их отправки клиенту:

.. code-block:: php

    <?php

    // Получение всех заголовков
    $headers = $response->getHeaders();

    // Получение заголовка по имени
    $contentType = $response->getHeaders()->get("Content-Type");

Создание перенаправлений (редиректы)
------------------------------------
С помощью :doc:`Phalcon\\HTTP\\Response <../api/Phalcon_Http_Response>` вы можете выполнять переадресовывания HTTP:

.. code-block:: php

    <?php

    // Переадресация на корневой URI
    $response->redirect();

    // Перенаправление на внутренний URI
    $response->redirect("posts/index");

    // Перенаправление на внешнюю ссылку
    $response->redirect("http://en.wikipedia.org", true);

    // Перенаправление со специальным HTTP кодом
    $response->redirect("http://www.example.com/new-location", true, 301);

Все ссылки обслуживаются внутренним сервисом 'url' (по умолчанию это :doc:`Phalcon\\Mvc\\Url <url>`), в таком случае вы можете использовать
перенапралвения на определённые в приложении маршруты (роуты):

.. code-block:: php

    <?php

    // Переадресация по именованному правилу роутинга
    $response->redirect(array(
        "for" => "index-lang",
        "lang" => "jp",
        "controller" => "index"
    ));

Обратите внимание, что при создании перенаправления не отключается компонент отображения (Views), так что действие в котором оно вызывается всё 
равно будет выполнено. Вы можете отключить отображение из контроллера, выполнив $this->view->disable();

HTTP кеширование
----------------
One of the easiest ways to improve the performance in your applications also reducing the traffic is the HTTP Cache.
Most modern browsers support HTTP caching and is one of the reasons why many websites are currently fast.

The secret are the headers sent by the application when serving a page for the first time, these headers are:

* *Expires:* With this header the application can set a date in the future or the past telling the browser when the page must expire.
* *Cache-Control:* This header allows to specify how much time a page should be considered fresh in the browser.
* *Last-Modified:* This header tells the browser which was the last time the site was updated avoiding page re-loads
* *ETag:* An etag is a unique identifier that must be created including the modification timestamp of the current page

Expires
^^^^^^^
Указание срока жизни является одним из наиболее удобных и эффективных способов кеширования страниц на стороне клиента (браузера).
Мы добавим дополнительный срок к текущему времени, это укажет браузеру сохранять страницу в кеше пока этот срок не истечет
и не обращаться за ней к серверу:

.. code-block:: php

    <?php

    $expireDate = new DateTime();
    $expireDate->modify('+2 months');

    $response->setExpires($expireDate);

Ответ в компоненте Response автоматически преобразует дату для временной зоны GMT, именно так как ожидается в заголовке Expires.

Более того, если мы укажем прошедшую дату, то это указыжзет браузеру всегда обновлять запрошенную страницу:

.. code-block:: php

    <?php

    $expireDate = new DateTime();
    $expireDate->modify('-10 minutes');

    $response->setExpires($expireDate);

Браузеры основываются на системных часах клиента для рассчета прошла эта дата или нет, часы на клиенте могут быть изменены и 
срок жизни будет некорректен. Это ограничение такого механизма кеширования.

Cache-Control
^^^^^^^^^^^^^
Этот заголовок осуществляет более безопасный способ кеширования. Мы просто указываем браузеру время в секундах на которое необходимо
хранить страницы в кеше:

.. code-block:: php

    <?php

    // Кешировать на сутки с текущего момента
    $response->setHeader('Cache-Control', 'max-age=86400');

Противоположный эффект (для запрета кеширования страницы) организуется следующим образом:

.. code-block:: php

    <?php

    // Не кешировать
    $response->setHeader('Cache-Control', 'private, max-age=0, must-revalidate');

E-Tag
^^^^^
Заголовок "entity-tag" или кратко "E-tag" позволяет браузеру понять, была ли изменена страница между двумя запросами.
Идентификатор должен рассчитываться таким образом, что бы измениться если изменено содержимое страницы:

.. code-block:: php

    <?php

    // Формирование значения E-Tag основанное на последнем времени изменения новости
    $recentDate = News::maximum(array('column' => 'created_at'));
    $eTag = md5($recentDate);

    // Отправка E-Tag
    $response->setHeader('E-Tag', $eTag);


