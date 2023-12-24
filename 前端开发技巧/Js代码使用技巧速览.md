### 借助构造函数和浮点数的误差来做数学运算

```javascript
Number((0.1 + 0.2).toFixed(1)) === 0.3; // true
```

### 使用“摊平参数”和“apply”做参数不固定的最值查找

```javascript
const nums = [5, 3, 9, 1, 6];
const maxNum = Math.max.apply(null, nums); // 9
const minNum = Math.apply(null, nums);     // 1
```

### 利用Array构造函数创建长度固定但值未定义的数组

```javascript
const arrayOfUndefined = Array(3); // [undefined, undefined, undefined]
```

### 使用位运算符进行整数的快捷操作

```javascript
// 快速地求平方
let i = 2;
let square = i << 1; // 等价于 i * 2 或 i ** 2

// 快速地从浮点数中丢弃小数部分取整
let floatNum = 3.15;
let intNum = floatNum | 0; // 3
```

### 利用void运算符来执行表达式且不返回结果

```javascript
void function iife() {
  var localVar = 'I am not returned';
  console.log(localVar);
}();

console.log(typeof localVar); // undefined
```

### 使用逗号运算符链式执行多个表达式

```javascript
let x = 1;
(x += 1, x *= 3);
console.log(x); // 6
```

### 使用标签模板语法进行高级字符串操作

```javascript
function highlight(strings, ...values) {
  return strings.reduce((acc, str, i) => `${acc}${str}<mark>${values[i] || ''}</mark>`, '');
}

const name = "Alice";
const greeting = highlight`Hello there, ${name}`;
console.log(greeting); // "Hello there,<mark>Alice</mark>"
```

### 利用 IIFE 和闭包保存状态

```javascript
var elems = document.querySelectorAll('select option:checked');
var values = Array.prototype.map.call(elems, function(obj){
  return obj.value;
});
```

### 使用数组解构来交换变量的值

```javascript
let a = 1, b = 2;
[b, a] = [a, b];
console.log(a); // 2
console.log(b); // 1
```

### 使用逻辑或为函数参数提供默认值

```javascript
function logName(name) {
  name = name || 'Unknown';
  console.log(name);
}
logName(); // 'Unknown'
logName('Alice'); // 'Alice'
```