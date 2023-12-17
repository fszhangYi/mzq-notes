# 7个Js async/await高级用法

JavaScript的异步编程已经从回调(Callback)演进到`Promise`，再到如今广泛使用的`async`/`await`语法。后者不仅让异步代码更加简洁，而且更贴近同步代码的逻辑与结构，大大增强了代码的可读性与可维护性。在掌握了基础用法之后，下面将介绍一些高级用法，以便充分利用`async`/`await`实现更复杂的异步流程控制。

## 1. `async/await`与高阶函数

当需要对数组中的元素执行异步操作时，可结合`async/await`与数组的高阶函数（如`map`、`filter`等）。

```javascript
// 异步过滤函数
async function asyncFilter(array, predicate) {
    const results = await Promise.all(array.map(predicate));

    return array.filter((_value, index) => results[index]);
}

// 示例
async function isOddNumber(n) {
    await delay(100); // 模拟异步操作
    return n % 2 !== 0;
}

async function filterOddNumbers(numbers) {
    return asyncFilter(numbers, isOddNumber);
}

filterOddNumbers([1, 2, 3, 4, 5]).then(console.log); // 输出: [1, 3, 5]
```

## 2. 控制并发数

在处理诸如文件上传等场景时，可能需要限制同时进行的异步操作数量以避免系统资源耗尽。

```javascript
async function asyncPool(poolLimit, array, iteratorFn) {
    const result = [];
    const executing = [];

    for (const item of array) {
        const p = Promise.resolve().then(() => iteratorFn(item, array));
        result.push(p);

        if (poolLimit <= array.length) {
            const e = p.then(() => executing.splice(executing.indexOf(e), 1));
            executing.push(e);
            if (executing.length >= poolLimit) {
                await Promise.race(executing);
            }
        }
    }

    return Promise.all(result);
}

// 示例
async function uploadFile(file) {
    // 文件上传逻辑
}

async function limitedFileUpload(files) {
    return asyncPool(3, files, uploadFile);
}
```

## 3. 使用`async/await`优化递归

递归函数是编程中的一种常用技术，`async/await`可以很容易地使递归函数进行异步操作。

```javascript
// 异步递归函数
async function asyncRecursiveSearch(nodes) {
    for (const node of nodes) {
        await asyncProcess(node);
        if (node.children) {
            await asyncRecursiveSearch(node.children);
        }
    }
}

// 示例
async function asyncProcess(node) {
    // 对节点进行异步处理逻辑
}
```

## 4. 异步初始化类实例

在JavaScript中，类的构造器（constructor）不能是异步的。但可以通过工厂函数模式来实现类实例的异步初始化。

```javascript
class Example {
    constructor(data) {
        this.data = data;
    }

    static async create() {
        const data = await fetchData(); // 异步获取数据
        return new Example(data);
    }
}

// 使用方式
Example.create().then((exampleInstance) => {
    // 使用异步初始化的类实例
});
```

## 5. 在`async`函数中使用`await`链式调用

使用`await`可以直观地按顺序执行链式调用中的异步操作。

```javascript
class ApiClient {
    constructor() {
        this.value = null;
    }

    async firstMethod() {
        this.value = await fetch('/first-url').then(r => r.json());
        return this;
    }

    async secondMethod() {
        this.value = await fetch('/second-url').then(r => r.json());
        return this;
    }
}

// 使用方式
const client = new ApiClient();
const result = await client.firstMethod().then(c => c.secondMethod());
```

## 6. 结合`async/await`和事件循环

使用`async/await`可以更好地控制事件循环，像处理DOM事件或定时器等场合。

```javascript
// 异步定时器函数
async function asyncSetTimeout(fn, ms) {
    await new Promise(resolve => setTimeout(resolve, ms));
    fn();
}

// 示例
asyncSetTimeout(() => console.log('Timeout after 2 seconds'), 2000);
```

## 7. 使用`async/await`简化错误处理

错误处理是异步编程中的重要部分。通过`async/await`，可以将错误处理的逻辑更自然地集成到同步代码中。

```javascript
async function asyncOperation() {
    try {
        const result = await mightFailOperation();
        return result;
    } catch (error) {
        handleAsyncError(error);
    }
}

async function mightFailOperation() {
    // 有可能失败的异步操作
}

function handleAsyncError(error) {
    // 错误处理逻辑
}
```

通过以上七个`async/await`的高级用法，开发者可以在JavaScript中以更加声明式和直观的方式处理复杂的异步逻辑，同时保持代码整洁和可维护性。在实践中不断应用和掌握这些用法，能够有效地提升编程效率和项目的质量。