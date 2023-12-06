在日常的前端开发中，JavaScript无疑是核心技术之一。JavaScript语言的灵活性和表现力，让它在各种前端场景下展现出惊人的活力和创造力。JavaScript的每一个特性都可以用来解锁前端的新姿态，恰当运用这些技巧，不仅可以提高代码的效率，还能令阅读者和使用者眼前一亮。

以下便是JavaScript（JS）中值得关注的30个用法，每个用法均有示例代码进行展示，以期能让代码效率和可读性得到提升。

### 1. 默认参数值
在ES6以前，若函数参数未传值则默认为`undefined`，但现在可以设置默认参数值。

```javascript
function greeting(name = "Anonymous") {
    return `Hello, ${name}!`;
}
console.log(greeting("Alice")); // "Hello, Alice!"
console.log(greeting());        // "Hello, Anonymous!"
```

### 2. 模板字符串
用反引号（` ` `）和`${}`，可以轻松拼接字符串和变量。

```javascript
const user = 'Jane';
console.log(`Hi, ${user}!`); // "Hi, Jane!"
```

### 3. 多行字符串
使用反引号，可直接创建多行字符串。

```javascript
const multiLine = `This is a string
that spans across
multiple lines.`;
console.log(multiLine);
```

### 4. 解构赋值
快速提取数组或对象中的值。

```javascript
const [a, b] = [1, 2];
const { x, y } = { x: 10, y: 20 };
console.log(a, b); // 1 2
console.log(x, y); // 10 20
```

### 5. 展开运算符
用于数组或对象中创建副本或合并。

```javascript
const numbers = [1, 2, 3];
const newNumbers = [...numbers, 4, 5];
console.log(newNumbers); // [1, 2, 3, 4, 5]
```

### 6. 剩余参数
函数可以接受不定数量的参数，作为一个数组。

```javascript
function sum(...args) {
    return args.reduce((total, current) => total + current, 0);
}
console.log(sum(1, 2, 3)); // 6
```

### 7. 箭头函数
更简洁的函数写法，自动绑定上下文 `this`。

```javascript
const add = (a, b) => a + b;
console.log(add(2, 3)); // 5
```

### 8. Promise 和异步
处理异步操作，支持链式调用。

```javascript
const fetchData = () => new Promise(resolve => setTimeout(() => resolve("Data"), 1000));
fetchData().then(data => console.log(data)); // "Data"
```

### 9. Async/Await
使异步代码看起来更像同步代码。

```javascript
const fetchData = async () => {
    const data = await new Promise(resolve => setTimeout(() => resolve("Data"), 1000));
    console.log(data);
};
fetchData();
```

### 10. 对象属性简写
创建对象时，若键和变量名相同，可省略键。

```javascript
const name = "Alice";
const age = 25;
const user = { name, age };
console.log(user); // { name: "Alice", age: 25 }
```

### 11. 计算属性名
在对象字面量中使用表达式作为属性名。

```javascript
const propName = "name";
const user = {
  [propName]: "Alice",
};
console.log(user.name); // "Alice"
```

### 12. 函数参数解构
直接在参数位置上解构对象或数组。

```javascript
function greet({ name, age }) {
    console.log(`Hello, my name is ${name} and I'm ${age} years old.`);
}
greet({ name: "Alice", age: 30 });
```

### 13. 对象方法简写
对象中的函数可以省略`function`关键字。

```javascript
const calculator = {
    add(a, b) {
        return a + b;
    },
};
console.log(calculator.add(2, 3)); // 5
```

### 14. 导入/导出模块
模块化JavaScript代码，让结构更清晰。

```javascript
// math.js
export function add(a, b) { return a + b; }

// main.js
import { add } from './math.js';
console.log(add(2, 3)); // 5
```

### 15. 类与构造函数
ES6引入了类的概念，使得面向对象编程更为直观。

```javascript
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    
    greet() {
        console.log(`Hello, my name is ${this.name}!`);
    }
}

const person = new Person('Alice', 30);
person.greet(); // "Hello, my name is Alice!"
```

### 16. 类的继承
类可以通过`extends`关键字继承其他类。

```javascript
class Animal {
    constructor(name) {
        this.name = name;
    }
    speak() {
        console.log(`${this.name} makes a noise.`);
    }
}

class Dog extends Animal {
    constructor(name) {
        super(name);
    }
    speak() {
        console.log(`${this.name} barks.`);
    }
}

const dog = new Dog('Rex');
dog.speak(); // "Rex barks."
```

### 17. Set集合
用于创建无重复值的集合。

```javascript
const mySet = new Set([1, 2, 3, 2, 1]);
console.log(mySet); // Set {1, 2, 3}
```

### 18. Map映射
类似于对象，但键的范围不限于字符串。

```javascript
const myMap = new Map();
myMap.set('a', 1);
myMap.set('b', 2);
console.log(myMap.get('a')); // 1
```

### 19. Array.from
将类数组或可迭代对象转换为数组。

```javascript
const nodeList = document.querySelectorAll('div');
const nodeArray = Array.from(nodeList);
console.log(nodeArray); // [div, div, ...]
```

### 20. Array.find & findIndex
查找数组中满足条件的第一个元素及其索引。

```javascript
const numbers = [5, 12, 8, 130, 44];
const found = numbers.find(number => number > 10);
console.log(found); // 12
```

### 21. 对象的entries方法
返回一个对象所有的键值对数组。

```javascript
const user = { name: 'Alice', age: 25 };
const entries = Object.entries(user);
console.log(entries); // [["name", "Alice"], ["age", 25]]
```

### 22. 对象的keys和values方法
分别获取对象的所有键或所有值。

```javascript
const user = { name: 'Alice', age: 25 };
const keys = Object.keys(user);
const values = Object.values(user);
console.log(keys); // ["name", "age"]
console.log(values); // ["Alice", 25]
```

### 23. 对象的fromEntries方法
将键值对列表转换为对象。

```javascript
const entries = [['name', 'Alice'], ['age', 25]];
const user = Object.fromEntries(entries);
console.log(user); // { name: "Alice", age: 25 }
```

### 24. 对象和数组的深拷贝
使用JSON方法进行对象和数组的深拷贝。

```javascript
const user = { name: 'Alice', age: 25 };
const userCopy = JSON.parse(JSON.stringify(user));
console.log(userCopy); // { name: "Alice", age: 25 }
```

### 25. 可选链操作符
安全地访问深层次对象属性，避免引起错误。

```javascript
const user = { name: 'Alice', address: { city: 'Wonderland' } };
console.log(user.address?.city); // "Wonderland"
console.log(user.address?.postcode); // undefined
```

### 26. 空值合并操作符
在左侧操作数为`null`或`undefined`时，返回右侧操作数。

```javascript
const nullValue = null;
const defaultValue = nullValue ?? 'default';
console.log(defaultValue); // "default"
```

### 27. Symbol类型
创建唯一标识符，用于对象属性以避免冲突。

```javascript
const symbol = Symbol('description');
console.log(symbol); // Symbol(description)
```

### 28. for...of 循环
用于遍历可迭代对象（如数组、Map、Set等）。

```javascript
const numbers = [1, 2, 3];
for (const number of numbers) {
    console.log(number);
}
// 1
// 2
// 3
```

### 29. for...in 循环
遍历对象的自身和继承的可枚举属性。

```javascript
const user = { name: 'Alice', age: 25 };
for (const key in user) {
    if (user.hasOwnProperty(key)) {
        console.log(`${key}: ${user[key]}`);
    }
}
// "name: Alice"
// "age: 25"
```

### 30. strict模式
可通过"use strict"指令开启严格模式，提高代码的严谨性。

```javascript
'use strict';
let undefinedVariable; // 未定义变量会抛出错误
```