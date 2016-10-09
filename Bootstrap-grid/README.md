# Mixin Bootstrap grid and respond.scss

Это расширение для Bootstrap Sass созданное для упрощения работы с Bootstrap сеткой и оптимизации CSS.
Проще говоря в CSS не попадают лишние класы.

Для использования в исполняемый файл необходимо подключить бутстраповские переменные, clearfix, responsive, и сам grid:

```sass
@import "bootstrap/variables";
@import "bootstrap/mixins/clearfix";
@import "mixins/responsive.scss";
@import "mixins/grid";
```


##Две реализации.

### Первая. Использование mixin:

```sass
.box {
	@include make-col(6, 4, 3, 2);
}
```
Аргументы - количество столбцов занимаемых в ряде начиная с xs, sm, md, lg.
Обязательным является только первый агрумент(xs, необъявленные будут скопированы).

```sass
@mixin make-col (
  $xs-col, 
  $sm-col: $xs-col, 
  $md-col: $sm-col, 
  $lg-col: $md-col )
{
```

### Вторая(рекомендованная). Использование @extend:

```sass
.box {
	@extend %col-xs-6;
	@extend %col-sm-4;
	@extend %col-md-3;
	@extend %col-lg-1;
```

При компиляции создаются шаблонные селекторы, которые в последствии можно использовать с директивой @extend.

В этом способе доступен основной бутстраповский функционал сетки, а именно: offset, pull, push. Например: 

```sass
.box {
	@extend %col-lg-offset-2;
		&:first-child {
			@extend %col-lg-offset-0;
	}
}
```
#### Преимущество 2й реализации:
- Создает единожды медиазапрос соответствующий ширине окна(отдельный для sm, md, lg), в который добавляеются классы, которые были расширены соответствующим шаблонным селектором. В 1й же реализации миксин просто создает новый media query для каждого класа.
- Доступны offset, pull, push.

#### Недостаток: 
Дублируются класы при каждом @extend:

```css
.box, .box, .box, .box {
  position: relative;
  min-height: 1px;
  padding-left: 15px;
  padding-right: 15px;
  float: left; 
 }
```	
 Но минимирзация его устраняет:

```css
.box{position:relative;min-height:1px;padding-left:15px;padding-right:15px;float:left}
```


## Расширение снабжено _responsive.scss для поддержки IE8. 

Для ИЕ компилируется отдельный файл со статической весткой. Значения соответствуют наибольшей ширене окна(lg -> md -> sm -> xs).


Обычно я создаю 2 файла - главный и для ИЕ, например BT-grid.scss и BT-grid-old.scss.


- В BT-grid.scss объявляю переменную со значением false по умолчанию и пишу весь код:

```sass
$fix-ie: false !default;
...
```
- В BT-grid-old.scss записываю всего 2 строки:

```sass
$fix-ie: true;
@import "BT-grid.scss";
```

В итоге компилится 2 файла - основной, с media queries, и IEшный со статикой.
Лишние класы, которые переопределяют сами себя, убираются при минимизации.

Пример использования: 

```html
<!--[if lte IE 8]>
<link rel="stylesheet" href="dist/styles/BT-grid-old.min.css">
<![endif]-->	
<!--[if gt IE 8]><!-->
<link rel="stylesheet" href="dist/styles/BT-grid.min.css">
<!--<![endif]-->
```
