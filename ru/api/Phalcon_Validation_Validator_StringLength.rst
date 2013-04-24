Класс **Phalcon\\Validation\\Validator\\StringLength**
======================================================

*расширение* :doc:`Phalcon\\Validation\\Validator <Phalcon_Validation_Validator>`

*реализация* :doc:`Phalcon\\Validation\\ValidatorInterface <Phalcon_Validation_ValidatorInterface>`

Проводит валидацию на указанное максимальное и минимальное ограничения длины строковой переменной

.. code-block:: php

    <?php

    use Phalcon\Validation\Validator\StringLength as StringLength;
    
    $validation->validate('name_last', new StringLength(array(
    'max' => 50,
    'min' => 2,
    'messageMaximum' => 'We don't like really long names',
    'messageMinimum' => 'We want more than just their initials'
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

