Класс **Phalcon\\Validation\\Validator\\InclusionIn**
=====================================================

*расширение* :doc:`Phalcon\\Validation\\Validator <Phalcon_Validation_Validator>`

*реализация* :doc:`Phalcon\\Validation\\ValidatorInterface <Phalcon_Validation_ValidatorInterface>`

Проверяет существует ли указанное значение в списке значений  

.. code-block:: php

    <?php

    use Phalcon\Validation\Validator\InclusionIn;
    
    $validator->add('status', new InclusionIn(array(
       'message' => 'The status must be A or B'
       'domain' => array('A', 'B')
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

