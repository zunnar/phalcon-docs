Формы
-----
Компонент Phalcon\Forms позволяет создавать и управлять формами вашего приложения.

Ниже представлен базовый пример работы с формами:

.. code-block:: php

	use Phalcon\Forms\Form,
		Phalcon\Forms\Element\Text;

	$form = new Form();

	$form->add(new Text("name"));

	$form->add(new Text("telephone"));

	$form->add(new Select("telephoneType", array(
		'H' => 'Home',
		'C' => 'Cell'
	)));

Элементы форм выводятся по указанным при создании имён:

.. code-block:: html+php

	<h1>Контакты</h1>

	<form method="post">

		<p>
			<label>Имя</label>
			<?php echo $form->render("name") ?>
		</p>

		<p>
			<label>Телефон</label>
			<?php echo $form->render("telephone") ?>
		</p>

		<p>
			<label>Тип телефона</label>
			<?php echo $form->render("telephoneType") ?>
		</p>

		<p>
			<input type="submit" value="Сохранить" />
		</p>

	</form>

Каждый элемент формы может быть настроен по желанию разработчика. Внутри компонент исполльзует возможности
:doc:`Phalcon\\Tag <../api/Phalcon_Tag>` для генерации HTML кода каждого документа, вы можете передавать дополнительные
html-атрибуты вторым параметром:

.. code-block:: html+php

	<p>
		<label>Имя</label>
		<?php echo $form->render("name", array('maxlength' => 30, 'placeholder' => 'Введите своё имя')) ?>
	</p>

Аттрибуты HTML могут быть указаны в параметрах при создании элемента:

.. code-block:: php

	$form->add(new Text("name", array(
		'maxlength' => 30,
		'placeholder' => 'Введите своё имя'
	)));

