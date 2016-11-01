# Mixin Bootstrap grid

Это расширение для Bootstrap Sass созданное для упрощения работы с Bootstrap сеткой и оптимизации CSS.
Проще говоря в CSS не попадают лишние селекторы.

Для использования в исполняемый файл необходимо подключить бутстраповские переменные, clearfix, и сам grid:

```sass
@import "bootstrap/variables";
@import "bootstrap/mixins/clearfix";
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

Пример:

```sass
.foo {
@include make-col(12,6,4);
}
```


### Вторая. Использование @extend:

```sass
.box {
	@extend %col-xs-6;
	@extend %col-sm-4;
	@extend %col-md-3;
	@extend %col-lg-1;
```

При компиляции создаются шаблонные селекторы, которые в последствии можно использовать с директивой @extend.

В этом способе доступен основной бутстраповский функционал сетки, а именно: offset, pull, push. 

Пример: 

```sass
.foo {
	@extend %col-xs-12;
	@extend %col-sm-10;
	@extend %col-sm-offset-2;
}
```

#### Преимущество 2й реализации:
- Создает единожды медиазапрос соответствующий ширине окна(отдельный для sm, md, lg), в который добавляеются классы, что были расширены соответствующим шаблонным селектором. В 1й реализации миксин просто создает новый media query для каждого селектора.
- Доступны offset, pull, push.

#### Недостаток: 
Дублируются селекторы при каждом @extend:

```css
.foo, .foo{
  position: relative;
  min-height: 1px;
  padding-left: 15px;
  padding-right: 15px;
  float: left; 
 }
```

 Но после минимирзации все ок.
