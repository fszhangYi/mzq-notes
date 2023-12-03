# 28个令人惊艳的JavaScript单行代码

JavaScript作为一种强大而灵活的脚本语言，充满了许多令人惊艳的特性。本文将带你探索25个令人惊艳的JavaScript单行代码，展示它们的神奇魅力。

### 1. 阶乘计算
使用递归函数计算给定数字的阶乘。
```javascript
const factorial = n => n === 0 ? 1 : n * factorial(n - 1);
console.log(factorial(5)); // 输出 120
```

### 2. 判断一个变量是否为对象类型
```javascript
const isObject = variable === Object(variable);
```

### 3. 数组去重
利用Set数据结构的特性，去除数组中的重复元素。
```javascript
const uniqueArray = [...new Set(array)];
```

### 4. 数组合并
合并多个数组，创建一个新的数组。
```javascript
const mergedArray = [].concat(...arrays);
```

### 5. 快速最大值和最小值
获取数组中的最大值和最小值。
```javascript
const max = Math.max(...array);
const min = Math.min(...array);
```

### 6. 数组求和
快速计算数组中所有元素的和。
```javascript
const sum = array.reduce((acc, cur) => acc + cur, 0);
```

### 7. 获取随机整数
生成一个指定范围内的随机整数。
```javascript
const randomInt = Math.floor(Math.random() * (max - min + 1)) + min;
```

### 8. 反转字符串
将字符串反转。
```javascript
const reversedString = string.split('').reverse().join('');
```

### 9. 检查回文字符串
判断一个字符串是否为回文字符串。
```javascript
const isPalindrome = string === string.split('').reverse().join('');
```

### 10. 扁平化数组
将多维数组转换为一维数组。
```javascript
const flattenedArray = array.flat(Infinity);
```

### 11. 取随机数组元素
从数组中随机取出一个元素。
```javascript
const randomElement = array[Math.floor(Math.random() * array.length)];
```

### 12. 判断数组元素唯一
检查数组中的元素是否唯一。
```javascript
const isUnique = array.length === new Set(array).size;
```

### 13. 字符串压缩
将字符串中重复的字符进行压缩。
```javascript
const compressedString = string.replace(/(.)\1+/g, match => match[0] + match.length);
```

### 14. 生成斐波那契数列
生成斐波那契数列的前n项。
```javascript
const fibonacci = Array(n).fill().map((_, i, arr) => i <= 1 ? i : arr[i - 1] + arr[i - 2]);
```

### 15. 数组求交集
获取多个数组的交集。
```javascript
const intersection = arrays.reduce((acc, cur) => acc.filter(value => cur.includes(value)));
```

### 16. 验证邮箱格式
检查字符串是否符合邮箱格式。
```javascript
const isValidEmail = /^[\w-]+(\.[\w-]+)*@([\w-]+\.)+[a-zA-Z]{2,7}$/.test(email);
```

### 17. 数组去除假值
移除数组中的所有假值，如`false`，`null`，`0`，`""`和`undefined`。
```javascript
const truthyArray = array.filter(Boolean);
```

### 18. 求阶乘
计算一个数的阶乘。
```javascript
const factorial = n => n <= 1 ? 1 : n * factorial(n - 1);
```

### 19. 判断质数
检查一个数是否为质数。
```javascript
const isPrime = n => ![...Array(n).keys()].slice(2).some(i => n % i === 0);
```

### 20. 检查对象是空对象
判断对象是否为空对象。
```javascript
const isEmptyObject = Object.keys(object).length === 0 && object.constructor === Object;
```

### 21. 判断回调函数为真
检查数组中的每个元素是否满足特定条件。
```javascript
const allTrue = array.every(condition);
```

### 22. 检查回调函数为假
检查数组中是否有元素满足特定条件。
```javascript
const anyFalse = array.some(condition);
```

### 23. 数组排序
对数组进行排序。
```javascript
const sortedArray = array.sort((a, b) => a - b);
```

### 24. 日期格式化
将日期对象格式化为指定格式的字符串。
```javascript
const formattedDate = new Date().toISOString().slice(0, 10);
```

### 25. 将字符串转为整数类型
```javascript
const intValue = +str;
```

### 26. 计算数组中元素出现的次数
统计数组中各元素的出现次数。
```javascript
const countOccurrences = array.reduce((acc, cur) => (acc[cur] ? acc[cur]++ : acc[cur] = 1, acc), {});
```

### 27. 交换两个变量的值
```javascript
[a, b] = [b, a];
```

### 28. 利用逗号运算符分隔多个表达式
```javascript
const result = (expression1, expression2, ..., expressionN);
```