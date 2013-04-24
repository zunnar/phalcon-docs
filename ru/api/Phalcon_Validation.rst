Класс **Phalcon\\Validation**
=============================




Методы
---------

public  **__construct** ([*array* $validators])

Phalcon\\Validation constructor



public  **validate** (*array|object* $data, [*object* $entity])

Проводит валидацию множество данных на множество правил



public *Phalcon\\Validator*  **add** (*string* $attribute, *unknown* $validator)

Добавляет валидатор к полю



public *array*  **getValidators** ()

Возвращает данные текущей переменной, которая пройшла валидацию



public *object*  **getEntity** ()

Возвращает связанный обьект



public :doc:`Phalcon\\Validation\\Message\\Group <Phalcon_Validation_Message_Group>`  **getMessages** ()

Возвращает зарегестрированные валидаторы



public  **appendMessage** (*Phalcon\\Validation\\MessageInterface* $message)

Добавляет запись в список сообщений



public *Phalcon\\Validator*  **bind** (*string* $entity, *string* $data)

Присваивает значение к обьекту. Обьект используется для получения значений прощедших валидацию



public *mixed*  **getValue** (*string* $attribute)

Получение значения для валидации из источников данных таких как массив/обьект



