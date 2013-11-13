Верстка
================================

Требования
----------

1.  Читабельность
2.  Простота поддержки
3.  Простота использования как при индивидуальной разработке сайта так и при работе в команде
4.  Совместная работа с кодом разных специалистов
5.  Повторное использование кода как внутри проекта, так и в других проектах

Основыные понятия
------
За основу взята разработанная в Яндексе методология [БЭМ] [1] (аббревиатура от слов Блок-Элемент-Модификатор). 

**Блок** - некоторая самостоятельная сущность, кирпичик проекта. 
*	Блок может быть **простым** либо **составным**, т.е. включать в себя другие блоки. 
*	Блок должен быть **независимым** и **самодостаточным**, т.е. он должен выглядеть однообразно, независимо от того в какой части страницы его отрисовали.
*	Имя блока должно быть уникальным в пределах проекта.

**Элемент** – это часть блока, отвечающая за отдельную его функцию. 
*	Элемент может находиться только в составе блока и не имеет смысла в отрыве от него. 
*	Имя элемента должно быть уникальным в пределах блока. 

**Модификатор** — это свойство блока или элемента, которое меняет внешний вид или поведение.

Оперируя "инструментами" HTML и CSS, основные понятия БЭМ можно интерпретировать следующим образом:
*	У блоков и элементов должны быть уникальные "имена", на которые можно написать CSS правило. Под “именем”, мы подразумеваем класс и только класс!
*	Не допускается использование id для именования, т.к. это исключает повторное использование блоков и элементов.
*	Имена классов должны однозначно описывать функционал блока. 
*	Не допускается использование HTML-тегов в качестве CSS-селекторов, т.к. они по определению контекстно-зависимые.
*	Необходимо избегать каскадные селекторы

Применение
----------

Для простоты восприятия, объясним основные понятия подхода на примере этой странички.

![alt text][pic_page]

Разобьем страницу на функциональные блоки: **“шапка”**, **“контент”**, **“подвал”**, **“меню”** и **“логотип”**. Тут главное без фанатизма, т.е не следует слишком сильно дробить страницу на блоки. Например блок “меню” - это “терминальная вершина”, и выделение блока “пункт меню” было бы излишне. Пункт меню - это элемент блока “меню”. А выделение активного пункта меню производится с помощью модификатора.

Рассмотрим пример именования CSS классов. 
 
1. Для блока "меню" определим класс `b-menu`. Класс описывает назначение блока и он уникален. Префикс `b-` - сокращение от block.
	```html
	<ul class=“b-menu”>
		...
	</ul>
	```

2. Класс элемента состоит из имени блока, которому он пренадлежит, и имени самого элемента, разделённых, например, двойным подчеркиванием. Необходимо учитывать имя блока в CSS-классе элемента для минимизации каскада.
	```html
	<ul class="b-menu">
	  	<li class="b-menu__item">
	  		<a href="#" class="b-menu__link">About</a>
	  	</li>
	  	<li class="b-menu__item">
	  		<a href="#" class="b-menu__link active">Blog</a>
	  	</li>
	  	...
	</ul>
	```

3. CSS-правила для блока должны быть абстрагированы от родительского элемента. Например позиционирование логотипа внутри блока “шапка” должно устанавливаться стилями “шапки”. Это позволит использовать логотип в других блоках с минимальными изменениями его стилей.
	```html
	<div class="b-header">
		<div class="b-header__logo">
			<a href="#" class="b-logo">Provectus IT</a>
		</div>
		<div class="b-header__menu">
			<ul class="b-menu">
				<li class="b-menu__item">
					<a href="#" class="b-menu__link">About</a>
				</li>
				<li class="b-menu__item">
					<a href="#" class="b-menu__link active">Blog</a>
				</li>
			</ul>
		</div>
	</div>
	``` 
	```css
	.b-header .b-header__logo {
	    float: left;
	    padding-left: 20px;
	}
	```
Шапка сайта содержит логотип выравненный по левой стороне. "Подвал" сайта тоже содержит логотип, при этом он прижат к правой стороне. В данной ситуации позиционированием логотипа должен заниматься блок родитель. В противном случае необходимо описывать классы модификаторы.

4. Минимальный уровень вложенности CSS-правил для элементов - 2, т.к. элемент может находиться только в составе блока и не имеет смысла в отрыве от него.
	```css
	.b-header .b-header__logo {
		float: left;
		padding-left: 20px;
	}
	```
5. Модификатор представляется дополнительным CSS-классом блока или элемента.
	```html
	<a href="#" class="b-menu__link active">Blog</a>
	```
Как показала практика модификаторы удобно разделить на две группы: 
	* состояния - их в "жизни" элемента может быть несколько (например: `active`, `current`, `disabled` и т.п.). Состояния удобнее описывать односложно не зависимо от контекста; 
	* мутации - изменяют вид элемента в момент отрисовки. Описываются в контексте блока (например: `b-menu__vertical` - модификатор, сообщающий блоку меню, что он должен отрисоваться столбиком, а не в строку)
Модификаторы могут накладываться друг на друга.

UI Kit. Структурные элементы
----------------------------
Строгое следование принципам БЭМ - это круто и сильно облегчает жизнь разработчику, но иногда имеет смысл немного отступить от правил.
Часто в своих проектах, мы выделяем еще одну немаловажную сущность - **структурные элементы**. 
**Структурные элементы** - это вспомогательные элементы, неоднократно используемые в проекте, но не являющиеся при этом блоками (таблицы, списки, кнопки, заголовки, поля ввода и т.п.). 
Чаще всего структуры входят в состав блоков и элементов. Их можно модифицировать в контексте блоков либо при помощи модификаторов (без фанатизма конечно). 
В случае координальных изменений создаем новую структуру или переносим ее описание в блок, если она используется единожды. Это очень тонкий момент, и все зависит от конкретного случая.

Особых отличий в именовании нет, мы просто меняем префикс класса с `b-` на `s-`(structure), например `s-short-list` или `s-btn`.
Относительно структур не возброняется привязка к HTML-тегам, дабы не перегружать код.
```css
.s-list li {
	list-style: none;
	border-bottom: 1px solid #ddd;
	...
}
```

Модификаторы работают так же как и в случае с блоками и элементами
```css
[class^="s-btn"],
[class*=" s-btn"] {
    border: 1px solid #eee;
    background: green;
    border-radius: 3px;
    opacity: 0.8;
    color: #fff;
    cursor: pointer;
    padding: 5px 10px;
}

[class^="s-btn"].active,
[class*=" s-btn"].active {
    opacity: 1;
}

.s-btn__success {
    background: blue;
}

.s-btn__danger {
    background: red;
}
```

Кроме структур, в больших проектах бывает удобно выделить layout-ы. Именование начинается с префикса `l-`. 
Layout не имеет элементов, а описывает некие глобальные правила. Например `font` или `background`.

Повторюсь, это очень тонкий момент! Набор инструментов зависит от поставленной задачи. 

JavaScript
----------

Часто, на начальных этапах проекта, основной функционал реализуется без верстки. Также бывают случаи глобального или локального редизайна. В таких ситуациях, для минифицирования конфликтов и недопониманий, важна возможность абстрагироваться от верстки.

С этими, и другими проблемами помогает боротся следуюшее правило: 
*	В качестве js селекторов, нельзя использовать элементы верстки. 

Для этих нужд создается специальный cелектор-класс с префиксом `.js-`, который в полной мере описывает его предназначение. Например: `.js-search-button` или `.js-confiramtion-popup`. 

[pic_page]: img/page.png "Logo Title Text 2"

[1]: http://bem.info/        "БЭМ"
  

