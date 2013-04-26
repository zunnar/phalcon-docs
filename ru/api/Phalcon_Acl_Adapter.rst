Класс **Phalcon\\Acl\\Adapter**
===============================

*реализации* :doc:`Phalcon\\Events\\EventsAwareInterface <Phalcon_Events_EventsAwareInterface>`

Адаптер для Phalcon\\Acl даптеров


Методы
---------

public  **setEventsManager** (:doc:`Phalcon\\Events\\ManagerInterface <Phalcon_Events_ManagerInterface>` $eventsManager)

Устанавливает менеджер событий



public :doc:`Phalcon\\Events\\ManagerInterface <Phalcon_Events_ManagerInterface>`  **getEventsManager** ()

Возвращает внутренний менеджер событий



public  **setDefaultAction** (*int* $defaultAccess)

Устанавливает уровень доступа по умолчанию (Phalcon\\Acl::ALLOW or Phalcon\\Acl::DENY)



public *int*  **getDefaultAction** ()

Получает уровень доступа ACL по умолчанию



public *string*  **getActiveRole** ()

Получает правило, из списка проверенных, которое дает доступ к определенным ресурсам / правам



public *string*  **getActiveResource** ()

Получить доступ к списку доступных ресурсов если таковые имеются



public *string*  **getActiveAccess** ()

Получить доступ к списку доступных правил если таковые имеются

