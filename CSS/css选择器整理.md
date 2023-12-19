# CSS选择器

CSS选择器是CSS的基石，赋予开发者精确控制HTML文档中元素样式的能力。选择器定义了CSS规则集将应用到HTML文档的哪一部分，通过不同类型的选择器，可以根据元素的tag、ID、class、属性、状态等各种条件来应用样式。本文将详细介绍各种CSS选择器并提供示例代码。

## 类型选择器（标签选择器）

类型选择器，也称为标签选择器，根据HTML元素的名称来选取元素。

```css
/* 所有的段落都会被应用样式 */
p {
  color: blue;
}
```

## id选择器

通过元素的ID属性选择特定的HTML元素。ID在HTML文档中是唯一的。

```css
/* 应用于ID为"header"的元素 */
#header {
  background-color: navy;
}
```

## 类选择器

类选择器通过HTML元素的class属性选取元素，一个class可以被多个元素共享。

```css
/* 所有带有"class-name"类的元素 */
.class-name {
  font-weight: bold;
}
```

## 后代选择器

后代选择器（也称为上下文选择器）用于选取某个元素的后代元素。

```css
/* 选择<div>内的所有<span> */
div span {
  color: red;
}
```

## 子选择器

子选择器仅选择直接子元素。

```css
/* 只选择<div>元素的直接子元素<ul> */
div > ul {
  list-style-type: none;
}
```

## 相邻同辈选择器

相邻同辈选择器选取紧随其后的相邻同辈元素。

```css
/* 选择<p>元素之后的第一个<h2>元素 */
p + h2 {
  margin-top: 0;
}
```

## 一般同辈选择器

一般同辈选择器用于选取某元素之后的所有具有相同父元素的同辈元素。

```css
/* 选择<h2>元素之后所有兄弟元素<h2> */
h2 ~ h2 {
  border-top: 1px solid gray;
}
```

## 通用选择器

通用选择器是一个通配符，匹配文档中的所有元素。

```css
/* 页面中所有元素都将有黄色背景 */
* {
  background: yellow;
}
```

## 属性选择器

属性选择器根据元素的属性及其值来选择元素。

```css
/* 选择含有"target"属性的HTML元素 */
[target] {
  border: 1px solid black;
}

/* 选择"target"属性等于"_blank"的HTML元素 */
[target="_blank"] {
  background-color: silver;
}
```

### 属性选择器与特殊字符

CSS还提供了特定的字符，供属性选择器使用，用于更加细致的条件匹配。

```css
/* 选择具有以"btn-"开头的class属性值的元素 */
[class^="btn-"] {
  padding: 5px 10px;
}

/* 选择具有以"-btn"结尾的class属性值的元素 */
[class$="-btn"] {
  font-weight: bold;
}

/* 选择class属性值含有"btn-"的任何地方的元素 */
[class*="btn-"] {
  font-size: 1.2em;
}
```

## 伪元素选择器

伪元素选择器用于样式化元素的特定部分或生成的内容。

### `::first-letter` 和 `::first-line`

```css
p::first-letter {
  font-size: 200%;
  color: #8A2BE2;
}

p::first-line {
  font-variant: small-caps;
}
```

### `::before` 和 `::after`

```css
h1::before {
  content: "Welcome ";
  color: green;
}

h1::after {
  content: "!";
  color: red;
}
```

## 伪类选择器

伪类选择器用于定义元素的特殊状态。

### `:link`、`:visited`、`:hover`、`:focus`、`:active`

```css
a:link {
  color: #FF0000;
}

a:visited {
  color: #00FF00;
}

a:hover {
  color: #FF00FF;
}

a:focus {
  border: 1px dashed #0000FF;
}

a:active {
  color: #0000FF;
}
```

### `:target` 和 `:not()`

```css
:target {
  background-color: #f9f9f9;
}

:not(.highlighted) {
  background-color: #ffff00;
}
```

## 结构化伪类选择器

结构化伪类选择器允许基于文档结构选择元素。

### `:nth-child` 和 `:nth-last-child`

```css
li:nth-child(odd) {
  color: red;
}

li:nth-child(even) {
  color: blue;
}

/* 从末尾开始的第二个子元素 */
li:nth-last-child(2) {
  font-size: 1.5em;
}
```

### `:nth-of-type` 和 `:nth-last-of-type`

```css
/* 选择第一个<p>类型的元素 */
p:nth-of-type(1) {
  font-weight: bold;
}

/* 选择倒数第二个<p>类型的元素 */
p:nth-last-of-type(2) {
  background-color: lightgray;
}
```

## 伪类表单选择器

CSS为表单提供了一组伪类选择器，用于根据不同的状态来设置样式。

### `:required`、`:valid`、`:invalid`、`:optional`、`:in-range` 和 `:out-of-range`

```css
input:required {
  border: 1px solid red;
}

input:valid {
  border: 1px solid green;
}

input:invalid {
  background-color: #ffdddd;
}

input:optional {
  opacity: 0.75;
}

input:in-range {
  border-color: blue;
}

input:out-of-range {
  border-color: orange;
}
```

### `:read-only` 和 `:read-write`

```css
input:read-only {
  background-color: #eeeeee;
}

input:read-write {
  background-color: #fff;
}
```

通过精确选择元素，CSS选择器几乎可以无限制地控制页面样式。了解并掌握它们，将使设计者能够有效地操作DOM结构，带来更丰富的表现力和互动性。随着CSS规范的不断发展，选择器的种类和功能也在不断扩展，进一步增强了开发者描述和实现复杂设计的能力。