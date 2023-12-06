# Less中鲜为人知但非常好用的方法

Less是一种动态样式语言，它扩展了CSS的静态特性，为前端开发者提供了更强大的功能和更高的代码复用性。在Less中，有许多强大但不邓为人知的特性，纵观深究，必能激发前端界的革新浪潮。本文展示几个鲜为人知的Less用法，通过示例代码加以说明，助力前端开发者迈向高效、简洁的样式编写境界。

### 1. 变量插值
变量不仅可以存储颜色、尺寸，也可以用于生成选择器或属性名。

```less
@btn: button;
@theme: dark;

.@{btn}-@{theme} {
  background-color: #333;
  color: #fff;
}

// 生成 button-dark 类
```

### 2. 混合（Mixin）无类调用
混合可以包含多重样式规则，而无需定义类名即可调用。

```less
.border-radius(@radius) {
  border-radius: @radius;
}

#header {
  .border-radius(10px);
}
```

### 3. 命名空间和访问器
命名空间可以将混合组织得更加模块化。

```less
#utils() {
  .center() {
    display: flex;
    justify-content: center;
    align-items: center;
  }
}

.box {
  #utils > .center();
}
```

### 4. 模式匹配
混合支持模式匹配，可以根据参数执行不同的代码块。

```less
.triangle(@direction, @size, @color) {
  border-style: solid;
  border-color: transparent;
  border-width: @size;
  @base: @size * 2;
  & when (@direction = top) {
    border-bottom-color: @color;
    border-bottom-width: @base;
  }
  // 更多方向的模式匹配...
}

.arrow {
  .triangle(top, 5px, #f00);
}
```

### 5. 函数式混合
调用混合时可以执行运算，使得混合更灵活。

```less
.padding(@value) {
  padding: @value * 2;
}

.container {
  .padding(5px);
}
```

### 6. Loop 循环
通过递归的方式实现循环，生成序列化的样式。

```less
.loop(@index) when (@index > 0) {
  (~".item-@{index}") { width: (@index * 20px); }
  .loop(@index - 1);
}
.loop(5);
```

### 7. 条件指令
可以使用`when`语句设定条件，使得代码逻辑更加灵活。

```less
.mixin(@type) when (@type = 'mobile') {
  font-size: 12px;
}
.mixin(@type) when (@type = 'desktop') {
  font-size: 16px;
}

.header {
  .mixin(mobile);
}
```

### 8. 颜色函数
Less提供了一系列处理颜色的函数，轻易调整颜色参数。

```less
@base-color: #4479BA;

.lighten-color {
  color: lighten(@base-color, 10%);
}
```

### 9. 数学函数
Less内置数学函数，简化复杂的计算。

```less
@base-padding: 3px;

.element {
  padding: multiply(@base-padding, 2);
}
```

### 10. 单位函数
转换数值的单位，或移除单位。

```less
.convert-units(@value, @unit) {
  @converted: unit(@value, @unit);
}

.value {
  width: .convert-units(10, rem);
}
```

### 11. 字符串插值
使用`~`可以输出字符串而不被Less处理。

```less
@font-face {
  font-family: 'OpenSans';
  src: url(~"@{assets-path}/fonts/OpenSans-Regular.ttf");
}
```

### 12. URL 编码
确保链接中的字符不会被错误解释。

```less
background-image: e(%("url(%s)", @image-path));
```

### 13. 作用域
变量的值由其在代码中的位置决定。

```less
@var: red;

.scope {
  @var: green;
  color: @var; // green
}
.outside {
  color: @var; // red
}
```

### 14. 引用父选择器 & 
与父选择器组合，常用于伪类和伪元素。

```less
a {
  color: blue;
  &:hover { color: green; }
}
```

### 15. 类型检查 Functions
检查变量的类型，返回boolean值。

```less
.is-number(@var) {
  return: isnumber(@var);
}

.check {
  is-number: .is-number(10px); // true
}
```

### 16. 映像函数
映射一组值到名称。

```less
#sizes {
  @small: 480px;
  @medium: 768px;
  @large: 1024px;
}

.get-size(@name) {
  (@size-name: ~'@{@{name}}');
  size: @@size-name;
}

.element {
  .get-size(small); // 480px
}
```

### 17. 映射循环
遍历映射key/value对。

```less
@themes: dark #333, light #fff, retro #fc0;

each(@themes, {
  .theme-@{key} { background-color: @value; }
});

// .theme-dark { background-color: #333; }
// .theme-light { background-color: #fff; }
// .theme-retro { background-color: #fc0; }
```

### 18. 表达式守卫
通过逻辑表达式决定是否调用混合。

```less
.mixin(@var) when (lightness(@var) > 50%) {
  background-color: dark(@var, 10%);
}

.light-element {
  .mixin(#ddd);
}
```

### 19. 默认变量值
使用`default()`函数为变量设置默认值。

```less
@variable: @other-variable, default(#ccc);

.element {
  color: @variable; // 如果 @other-variable 未定义，则使用 #ccc
}
```

### 20. 插件
可以通过插件扩展Less的能力。

```less
@plugin "plugin-name";

.use-plugin(/* plugin specific code */);
```

Less的这些用法在日常的开发工作中使用并不多，但深度掌握它们将使得样式表的编写更为流畅、高效，并且不失灵活。