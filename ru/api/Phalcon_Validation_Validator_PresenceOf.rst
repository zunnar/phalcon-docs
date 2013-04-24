Класс **Phalcon\\Validation\\Validator\\PresenceOf**
====================================================

*расширение* :doc:`Phalcon\\Validation\\Validator <Phalcon_Validation_Validator>`

*реализация* :doc:`Phalcon\\Validation\\ValidatorInterface <Phalcon_Validation_ValidatorInterface>`

Валидация значения строковой переменной на null или пустоту

.. code-block:: php

    <?php

    use Phalcon\Validation\Validator\PresenceOf;
    
    $validator->add('name', new PresenceOf(array(
       'message' => 'The name is required'
    )));



Методы
---------

public *boolean*  **validate** (:doc:`Phalcon\\Validation <Phalcon_Validation>` $validator, *string* $attribute)

Выполняет валидацию



public  **__construct** ([*array* $options]) inherited from Phalcon\\Validation\\Validator

Phalcon\\Validation\\Validator constructor



public *mixed*  **isSetOption** (*string* $key) inherited from Phalcon\\Validation\\Validator

Проверка, на определение параметров



public *mixed*  **getOption** (*string* $key) inherited from Phalcon\\Validation\\Validator

Возвращает значение определенного параметра, если же параметер не определен возращает null

