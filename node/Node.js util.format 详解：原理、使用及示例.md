# Node.js `util.format` 详解：原理、使用及示例

## `util.format` 简介

Node.js 的 `util` 模块为编程和调试提供了实用工具。其中的一个关键函数是 `util.format`，它类似于其他编程语言中的 `printf`。它用于格式化字符串，将占位符替换为提供的值。

## 原理

`util.format` 接受一个包含零个或多个占位符（`%s`、`%d`、`%j` 等）的格式字符串，后跟用于替换这些占位符的参数。每个占位符对应特定的类型：

- `%s` - 字符串
- `%d` - 数字（整数和浮点数）
- `%j` - JSON
- `%o` - 对象
- `%%` - 单个百分号（'%'）

如果传递的参数比占位符多，`util.format` 会将它们作为字符串连接起来，用空格分隔。

## 使用方法

### 基本使用

首先，引入 `util` 模块：

```javascript
const util = require('util');
```

然后使用 `util.format` 来格式化字符串：

```javascript
const formattedString = util.format('我的 %s 有 %d 个苹果', '篮子', 5);
console.log(formattedString); // 我的篮子有 5 个苹果
```

### 占位符

- 字符串占位符 `%s`：

  ```javascript
  console.log(util.format('%s', 'Node.js')); // Node.js
  ```

- 数字占位符 `%d`：

  ```javascript
  console.log(util.format('%d + %d = %d', 5, 10, 5 + 10)); // 5 + 10 = 15
  ```

- JSON 占位符 `%j`：

  ```javascript
  console.log(util.format('%j', { name: 'Node.js' })); // {"name":"Node.js"}
  ```

- 对象占位符 `%o`：

  ```javascript
  console.log(util.format('%o', { name: 'Node.js' })); // { name: 'Node.js' }
  ```

### 处理额外参数

当提供了没有对应占位符的额外参数时：

```javascript
console.log(util.format('Hello', 'World', '!')); // Hello World !
```

## 模仿 `util.format` 功能

要创建一个简化版的 `util.format`，可以使用 JavaScript 的模板字符串和正则表达式：

```javascript
function customFormat(format, ...args) {
  let i = 0;
  return format.replace(/%s|%d|%j|%o|%%/g, (match) => {
    if (match === '%%') return '%';
    return args[i++];
  }) + ' ' + args.slice(i).join(' ');
}

console.log(customFormat('我的 %s 有 %d 个苹果', '篮子', 5)); // 我的篮子有 5 个苹果
```

这个函数通过遍历格式字符串并用相应的参数替换每个占位符，复制了 `util.format` 的基本功能。

## 结论

Node.js 中的 `util.format` 是一个多功能的字符串格式化工具，提供了一种简单有效的方式将变量注入字符串。虽然原生函数提供了广泛的格式化选项，但定制实现可以根据特定需求进行调整，并有助于加深对 JavaScript 字符串格式化工作原理的理解。

---

# English version: Detailed Exploration of Node.js `util.format`: Principle, Usage, and Examples

## Introduction to `util.format`

Node.js's `util` module provides utilities for programming and debugging. One of its key functions is `util.format`, which is a formatting tool similar to `printf` in other programming languages. It formats a string, replacing placeholders with provided values.

## Principle

`util.format` takes a format string containing zero or more placeholders (`%s`, `%d`, `%j`, etc.) followed by arguments to replace those placeholders. Each placeholder corresponds to a specific type:

- `%s` - String
- `%d` - Number (both integer and floating-point)
- `%j` - JSON
- `%o` - Object
- `%%` - Single percent sign ('%')

If there are more arguments passed than placeholders, `util.format` concatenates them as strings, separated by spaces.

## Usage

### Basic Usage

First, require the `util` module:

```javascript
const util = require('util');
```

Then use `util.format` to format strings:

```javascript
const formattedString = util.format('My %s has %d apples', 'basket', 5);
console.log(formattedString); // My basket has 5 apples
```

### Placeholders

- String Placeholder `%s`:

  ```javascript
  console.log(util.format('%s', 'Node.js')); // Node.js
  ```

- Number Placeholder `%d`:

  ```javascript
  console.log(util.format('%d + %d = %d', 5, 10, 5+10)); // 5 + 10 = 15
  ```

- JSON Placeholder `%j`:

  ```javascript
  console.log(util.format('%j', { name: 'Node.js' })); // {"name":"Node.js"}
  ```

- Object Placeholder `%o`:

  ```javascript
  console.log(util.format('%o', { name: 'Node.js' })); // { name: 'Node.js' }
  ```

### Handling Extra Arguments

When extra arguments are provided without corresponding placeholders:

```javascript
console.log(util.format('Hello', 'World', '!')); // Hello World !
```

## Mimicking `util.format` Functionality

To create a simplified version of `util.format`, you can use JavaScript's template literals and regular expressions:

```javascript
function customFormat(format, ...args) {
  let i = 0;
  return format.replace(/%s|%d|%j|%o|%%/g, (match) => {
    if (match === '%%') return '%';
    return args[i++];
  }) + ' ' + args.slice(i).join(' ');
}

console.log(customFormat('My %s has %d apples', 'basket', 5)); // My basket has 5 apples
```

This function replicates the basic functionality of `util.format` by iterating over the format string and replacing each placeholder with the corresponding argument.

## Conclusion

`util.format` in Node.js is a versatile tool for string formatting, providing a simple and effective way to inject variables into strings. While the native function offers a range of formatting options, a custom implementation can be tailored to specific needs and helps deepen the understanding of how string formatting works in JavaScript.