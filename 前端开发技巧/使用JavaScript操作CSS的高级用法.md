# 使用JavaScript操作CSS的高级用法

Web开发中，JavaScript与CSS的结合用于增强网页的交互性和用户体验。JavaScript提供了丰富的API来操作CSS，实现动态风格调整、布局改变以及动画效果等。本文将探索几种高级方法来使用JavaScript操作CSS，并分别通过代码示例进行讲解。

## 类操作

JavaScript通过`classList`属性为DOM元素的类操作提供一系列方法。

### 添加、删除和切换类

```javascript
var element = document.getElementById('my-element');

// 添加类
element.classList.add('new-class');

// 删除类
element.classList.remove('old-class');

// 切换类（如果存在则删除，不存在则添加）
element.classList.toggle('toggle-class');

// 检查是否包含类
if (element.classList.contains('some-class')) {
    // 执行相关的操作
}
```

使用`classList`的方法进行类的操作比直接操作`className`属性更方便，有更好的可读性，并且可以避免覆写其他已存在的类。

## 内联样式操作

直接通过元素的`style`属性来设置内联样式。
```javascript
var element = document.getElementById('my-element');

// 设置内联样式
element.style.color = 'blue';
element.style.marginTop = '20px';
element.style.transform = 'translateX(50px)';

// 获取内联样式
var elementColor = element.style.color;

// 移除内联样式
element.style.removeProperty('color');
```

内联样式的操作可以针对单个元素进行精确控制，但也应该注意它会覆盖通过CSS类或选择器设置的样式。

## 计算样式获取

使用`window.getComputedStyle()`方法获取元素的计算后样式，此方法返回的是一个CSS样式声明对象。

```javascript
var element = document.getElementById('my-element');
var styles = window.getComputedStyle(element);

// 获取特定的样式
var elementColor = styles.getPropertyValue('color');
var elementWidth = styles.width; // 或者styles.getPropertyValue('width')
```

`getComputedStyle()`是获取当前元素所有最终使用的CSS属性值，包括由样式表隐式设置的属性值。

## CSS变量

可以通过JavaScript操作CSS变量（自定义属性），使得样式具备更高级的复用性和动态调整能力。

```javascript
var root = document.documentElement;

// 设置--main-bg-color变量
root.style.setProperty('--main-bg-color', 'coral');

// 获取--main-bg-color变量
var bgColor = root.style.getPropertyValue('--main-bg-color');
```

CSS变量可在全局作用域（`:root`）或任何DOM元素上设置。

## 样式表操作

JavaScript可以编辑、添加或删除文档中的样式表和CSS规则。

### 动态插入样式表

```javascript
var styleSheet = document.createElement('style');
styleSheet.type = 'text/css';
styleSheet.innerText = 'body { background-color: #f3f3f3; }';
document.head.appendChild(styleSheet);
```

以上代码创建了一个新的`<style>`元素，并向其添加CSS规则，然后将其插入到`<head>`中。

### 修改现有样式规则

```javascript
var sheet = document.styleSheets[0];
var rules = sheet.cssRules || sheet.rules; // 跨浏览器兼容

// 修改第一个CSS规则
if (rules.length > 0) {
    var firstRule = rules[0];
    if (firstRule.style) {
        firstRule.style.backgroundColor = 'lightblue';
    }
}

// 添加新规则
sheet.insertRule('p { font-size: 18px; }', rules.length);

// 删除规则
sheet.deleteRule(0);
```

对现有的样式表操作需要注意跨浏览器的细微差异，例如`cssRules`和`rules`的使用。

## CSS动画与Transitions

JavaScript可以用来控制CSS动画和过渡。

### 控制CSS Transitions

```javascript
var element = document.getElementById('my-element');

element.addEventListener('transitionend', function() {
    console.log('Transition 完成！');
});

// 触发过渡
element.style.width = '200px';
```

监听`transitionend`事件来获取过渡完成的时刻。

### 控制CSS Animations

```javascript
var element = document.getElementById('my-element');

element.addEventListener('animationstart', animationStartHandler);
element.addEventListener('animationend', animationEndHandler);
element.addEventListener('animationiteration', animationIterationHandler);

function animationStartHandler(event) {
    console.log('Animation 开始：', event.animationName);
}

function animationEndHandler(event) {
    console.log('Animation 结束：', event.animationName);
}

function animationIterationHandler(event) {
    console.log('Animation 迭代：', event.animationName);
}

// 启动动画
element.classList.add('run-animation');
```

监听动画相关事件来获取动画生命周期的各个阶段。

## 性能考虑

在操作CSS时，应注意对DOM的操作可能导致页面的回流（reflow）或重绘（repaint），这两者都有可能影响页面性能。

为了提高性能，以下几点建议值得考虑：

- **使用类而不是直接修改样式**：修改类比直接操作`style`属性性能要好，因为浏览器会针对类选择器的CSS修改优化。
- **集中样式变更**：对元素样式进行多项更改时，可以使用`DocumentFragment`、`CSSStyleSheet`或`CSSRule` API，或者通过分离元素进行离屏修改。
- **避免频繁的样式计算**：减少对`getComputedStyle`的调用，使用缓存值代替。
- **使用`requestAnimationFrame`**：在进行动画或持续的样式变更时使用`requestAnimationFrame`。

JavaScript操作CSS是一项强大的功能，极大地拓展了网页样式控制的能力。然而，这也对开发者提出了更高的要求，既要实现丰富多彩的功能，又要确保操作的性能和效率。通过上述介绀的方法和实践，应当能够有效地利用JavaScript对CSS进行高级操作，同时保持对性能的关注。