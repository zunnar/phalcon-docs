Заголовки ответа (Responses)
============================

Part of the HTTP cycle is return responses to the clients. :doc:`Phalcon\\HTTP\\Response <../api/Phalcon_Http_Response>` is the Phalcon
component designed to achieve this task. HTTP responses are usually composed by headers and body. The basic usage is the following:

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

Keep in mind that if you're using the full MVC stack there is no need to create responses manually. However, if you need to return a responde
directly from a controller's action follow this example:

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
Headers are an important part of the whole HTTP response. It contains useful information about the response state like the HTTP status,
type of response and much more.

Указывать заголовки можно следующим образом:

.. code-block:: php

    <?php

    // Установка по имени
    $response->setHeader("Content-Type", "application/pdf");
    $response->setHeader("Content-Disposition", 'attachment; filename="downloaded.pdf"');

    // Установка напрямую
    $response->setRawHeader("HTTP/1.1 200 OK");

A :doc:`Phalcon\\HTTP\\Response\\Headers <../api/Phalcon_Http_Response_Headers>` bag internally manages headers. This class
allows to manage headers before sending it to client:

.. code-block:: php

    <?php

    // Получение всех заголовков
    $headers = $response->getHeaders();

    // Получение заголовка по имени
    $contentType = $response->getHeaders()->get("Content-Type");

Создание перенаправлений (редиректы)
------------------------------------
С помощью :doc:`Phalcon\\HTTP\\Response <../api/Phalcon_Http_Response>` вы можите выполнять переадресовывания HTTP:

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

All internal URIs are generated using the 'url' service (by default :doc:`Phalcon\\Mvc\\Url <url>`), in this way you can make redirections
based on the routes you've currently defined in the application:

.. code-block:: php

    <?php

    // Редирект по именованному правилу роутинга
    $response->redirect(array(
        "for" => "index-lang",
        "lang" => "jp",
        "controller" => "index"
    ));

Note that making a redirection doesn't disable the view component, so if there is a view asociated with the current action it
will be executed anyway. You can disable the view from a controller by executing $this->view->disable();

HTTP кеш
--------
One of the easiest ways to improve the performance in your applications also reducing the traffic is the HTTP Cache.
Most modern browsers support HTTP caching and is one of the reasons why many websites are currently fast.

The secret are the headers sent by the application when serving a page for the first time, these headers are:

* *Expires:* With this header the application can set a date in the future or the past telling the browser when the page must expire.
* *Cache-Control:* This header allows to specify how much time a page should be considered fresh in the browser.
* *Last-Modified:* This header tells the browser which was the last time the site was updated avoiding page re-loads
* *ETag:* An etag is a unique identifier that must be created including the modification timestamp of the current page

Expires
^^^^^^^
The expiration date is one of the most easy and effective ways to cache a page in the client (browser).
Starting from the current date we add over time, then, this will maintain the page stored
in the browser cache until this date expires without requesting the content to the server again:

.. code-block:: php

    <?php

    $expireDate = new DateTime();
    $expireDate->modify('+2 months');

    $response->setExpires($expireDate);

The Response component automatically shows the date in GMT timezone in order as is expected in an Expires header.

Moreover if we set a date in the past this will tell the browser to always refresh the requested page:

.. code-block:: php

    <?php

    $expireDate = new DateTime();
    $expireDate->modify('-10 minutes');

    $response->setExpires($expireDate);

Browsers relies on the client's clock to assess if this date has passed or not, the client clock can be modified to
make pages expire, this may represent a limitation for this cache mechanism.

Cache-Control
^^^^^^^^^^^^^
This header provides a safer way to cache the pages served. We simply must specify a time in seconds telling the browser
how much time it must keep the page in its cache:

.. code-block:: php

    <?php

    // Кешировать на сутки с текущего момента
    $response->setHeader('Cache-Control', 'max-age=86400');

The opposite effect (avoid page caching) is achieved in this way:

.. code-block:: php

    <?php

    // Не кешировать
    $response->setHeader('Cache-Control', 'private, max-age=0, must-revalidate');

E-Tag
^^^^^
A "entity-tag" or "E-tag" is a unique identifier that helps the browser to realize if the page has changed or not between two requests.
The identifier must be calculated taking into account that this must change if the content has changed previously served:

.. code-block:: php

    <?php

    //Calculate the E-Tag based on the modification time of the latest news
    $recentDate = News::maximum(array('column' => 'created_at'));
    $eTag = md5($recentDate);

    // Отправка E-Tag
    $response->setHeader('E-Tag', $eTag);


