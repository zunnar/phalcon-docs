Класс **Phalcon\\Validation\\Validator\\Email**
===============================================

*расширение* :doc:`Phalcon\\Validation\\Validator <Phalcon_Validation_Validator>`

*реализация* :doc:`Phalcon\\Validation\\ValidatorInterface <Phalcon_Validation_ValidatorInterface>`

Проверяет правильно ли введен почтовый адрес

.. code-block:: php

    <?php

    use Phalcon\Validation\Validator\Email as EmailValidator;
    
    $validator->add('email', new EmailValidator(array(
       'message' => 'The e-mail is not valid'
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

