# 20个你不得不知道的Js async/await用法

JavaScript的`async`和`await`关键词是现代JavaScript异步编程的核心。它们让异步代码看起来和同步代码几乎一样，使得异步编程变得更加直观和易于管理。本文介绍20个关于`async/await`的实用技巧，将大大提升编程效率和代码的清晰度。

## 1. 基础用法
`async`函数返回一个`Promise`，而`await`关键词可以暂停`async`函数的执行，等待`Promise`解决。

```javascript
async function fetchData() {
    let data = await fetch('url');
    data = await data.json();
    return data;
}
```

## 2. 错误处理
使用`try...catch`结构处理`async/await`中的错误。

```javascript
async function fetchData() {
    try {
        let response = await fetch('url');
        response = await response.json();
        return response;
    } catch (error) {
        console.error('Fetching data error:', error);
    }
}
```

## 3. 并行执行
`Promise.all()`可以用来并行执行多个`await`操作。

```javascript
async function fetchMultipleUrls(urls) {
    const promises = urls.map(url => fetch(url).then(r => r.json()));
    return await Promise.all(promises);
}
```

## 4. 条件异步
根据条件执行`await`。

```javascript
async function fetchData(condition) {
    if (condition) {
        return await fetch('url');
    }
    return 'No fetch needed';
}
```

## 5. 循环中的`await`
在循环中使用`await`时，每次迭代都会等待。

```javascript
async function sequentialStart(urls) {
    for (const url of urls) {
        const response = await fetch(url);
        console.log(await response.json());
    }
}
```

## 6. 异步迭代器
对于异步迭代器（例如Node.js中的Streams），可以使用`for-await-of`循环。

```javascript
async function processStream(stream) {
    for await (const chunk of stream) {
        console.log(chunk);
    }
}
```

## 7. `await`之后立即解构
直接在`await`表达式后使用解构。

```javascript
async function getUser() {
    const { data: user } = await fetch('user-url').then(r => r.json());
    return user;
}
```

## 8. 使用默认参数避免无效的`await`
如果`await`可能是不必要的，可以使用默认参数避免等待。

```javascript
async function fetchData(url = 'default-url') {
    const response = await fetch(url);
    return response.json();
}
```

## 9. `await`在类的方法中
在类的方法中使用`async/await`。

```javascript
class DataFetcher {
    async getData() {
        const data = await fetch('url').then(r => r.json());
        return data;
    }
}
```

## 10. 立刻执行的`async`箭头函数
可以立即执行的`async`箭头函数。

```javascript
(async () => {
    const data = await fetch('url').then(r => r.json());
    console.log(data);
})();
```

## 11. 使用`async/await`进行延时
利用`async/await`实现延时。

```javascript
function delay(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
}

async function delayedLog(item) {
    await delay(1000);
    console.log(item);
}
```

## 12. 使用`async/await`处理事件监听器
在事件处理函数中使用`async/await`。

```javascript
document.getElementById('button').addEventListener('click', async (event) => {
    event.preventDefault();
    const data = await fetch('url').then(r => r.json());
    console.log(data);
});
```

## 13. 以顺序方式处理数组
使用`async/await`以确定的顺序处理数组。

```javascript
async function processArray(array) {
    for (const item of array) {
        await delayedLog(item);
    }
    console.log('Done!');
}
```

## 14. 组合`async/await`与`destructuring`以及`spread`运算符
结合使用`async/await`，解构和展开操作符。

```javascript
async function getConfig() {
    const { data, ...rest } = await fetch('config-url').then(r => r.json());
    return { config: data, ...rest };
}
```

## 15. 在对象方法中使用`async/await`
将`async`方法作为对象的属性。

```javascript
const dataRetriever = {
    async fetchData() {
        return await fetch('url').then(r => r.json());
    }
};
```

## 16. 异步生成器函数
使用`async`生成器函数结合`yield`。

```javascript
async function* asyncGenerator(array) {
    for (const item of array) {
        yield await processItem(item);
    }
}
```

## 17. 使用顶级`await`
在模块顶层使用`await`（需要特定的JavaScript环境支持）。

```javascript
// ECMAScript 2020引入顶级await特性, 部署时注意兼容性
const config = await fetch('config-url').then(r => r.json());
```

## 18. `async/await`与IIFE结合
将`async`函数与立即执行函数表达式（IIFE）结合。

```javascript
(async function() {
    const data = await fetch('url').then(r => r.json());
    console.log(data);
})();
```

## 19. 使用`async/await`优化递归调用
优化递归函数。

```javascript
async function asyncRecursiveFunction(items) {
    if (items.length === 0) return 'done';
    const currentItem = items.shift();
    await delay(1000);
    console.log(currentItem);
    return asyncRecursiveFunction(items);
}
```

## 20. 在`switch`语句中使用`await`
在`switch`语句的每个`case`中使用`await`。

```javascript
async function fetchDataBasedOnType(type) {
    switch (type) {
        case 'user':
            return await fetch('user-url').then(r => r.json());
        case 'post':
            return await fetch('post-url').then(r => r.json());
        default:
            throw new Error('Unknown type');
    }
}
```