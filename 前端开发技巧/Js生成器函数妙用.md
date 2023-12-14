# JS生成器函数妙用

生成器函数在JavaScript中是一种可以自定义迭代器行为的强大特性。生成器函数允许暂停函数的执行，保持函数内部状态，以便在必要时候可以恢复执行。这一特性使得它在处理复杂逻辑、流控制和异步编程中有着广泛的应用。

## 生成器函数的基础

一个生成器函数由`function*`语法和内部的`yield`表达式构成。当执行到`yield`时，函数执行将被暂停并返回一个包含yielded值的对象。该函数的执行可通过生成器的`.next()`方法继续。

```javascript
function* simpleGenerator() {
  yield 1;
  yield 2;
  yield 3;
}

const generatorObject = simpleGenerator();

console.log(generatorObject.next().value); // 1
console.log(generatorObject.next().value); // 2
console.log(generatorObject.next().value); // 3
```

接下来将展示7个生成器函数的妙用场景及相关代码。

## 场景一：遍历树结构

遍历树形数据结构时，生成器函数能够极大地简化代码。

```javascript
function* treeTraversal(tree) {
  yield tree.value;
  if (tree.children) {
    for (const child of tree.children) {
      yield* treeTraversal(child);
    }
  }
}

const tree = {
  value: 'root',
  children: [
    { value: 'child1' },
    { value: 'child2', children: [{ value: 'grandchild' }] }
  ]
};

for (const value of treeTraversal(tree)) {
  console.log(value);
}
```

## 场景二：控制流管理

生成器可以用于直观地处理复杂的控制流。

```javascript
function* flowControlGenerator() {
  const first = yield 'Need input 1';
  console.log(first);

  const second = yield 'Need input 2';
  console.log(second);

  return 'Done';
}

const controller = flowControlGenerator();

console.log(controller.next()); // { value: 'Need input 1', done: false }
console.log(controller.next('Input 1 provided')); // logs Input 1 provided
console.log(controller.next('Input 2 provided')); // logs Input 2 provided
```

## 场景三：暂停和恢复代码执行

在复杂的异步逻辑中，生成器允许我们暂停和恢复代码的执行，特别是在等待异步操作完成时。

```javascript
function* asyncDataFetcher() {
  const data = yield fetchDataFromAPI(); // fetchDataFromAPI() is an async function
  console.log(data);
}

const fetcher = asyncDataFetcher();
fetcher.next().value.then((data) => {
  fetcher.next(data);
});
```

## 场景四：无限序列生成

生成器函数可以创建无限的序列，而不需要存储整个序列。

```javascript
function* infiniteSequence() {
  let i = 0;
  while (true) {
    yield i++;
  }
}

const sequence = infiniteSequence();
console.log(sequence.next().value); // 0
console.log(sequence.next().value); // 1
// ... 无限执行下去，每次调用返回一个新值
```

## 场景五：搭建通信管道

生成器可以实现两个函数间的双向通信。

```javascript
function* generatorA() {
  const valueFromB = yield 'Data from A';
  console.log('Received data from B:', valueFromB);
}

function* generatorB(genA) {
  const valueFromA = genA.next().value;
  genA.next('Data from B');
  console.log('Received data from A:', valueFromA);
}

const genA = generatorA();
const genB = generatorB(genA);

genB.next();
```

## 场景六：批量处理任务

对于处理数据的任务可以分批次进行，以节省内存占用。

```javascript
function* batchProcessor(batchSize) {
  let data = null;
  while (data = fetchData(batchSize)) {
    yield processBatch(data);
  }
}

const processor = batchProcessor(100);
let processedData = processor.next();
while (!processedData.done) {
  console.log('Processed batch:', processedData.value);
  processedData = processor.next();
}
```

## 场景七：生成唯一ID

生成器可以用于生成唯一ID序列。

```javascript
function* uniqueIdGenerator() {
  let id = Date.now();
  while (true) {
    yield id++;
  }
}

const ids = uniqueIdGenerator();
console.log(ids.next().value); // 一个基于时间戳的ID
console.log(ids.next().value); // 下一个ID
```

生成器函数之所以能够解决实际业务中的问题，主要在于它们提供了一种处理数据和控制流的新方法。利用生成器的特性，能够编写出既高效又简洁的代码，特别适合处理数据集合、异步任务和复杂逻辑。在现代的JavaScript应用程序设计中，生成器的这些妙用场景可以大幅提升代码的可读性和功能性。然而，生成器的使用也需要注意，它可能会引入新的抽象层面，并不总是对代码的性能或逻辑清晰度有直接的帮助。在实际中选用生成器作为解决方案之前，要充分评估问题的本质及其他可行的实现方式。