# JS中数组的sort鲜为人知但极其好用的做法

JavaScript 中数组的排序方法 `sort()` 是处理数组序列化问题时经常被调用的工具。本文揭秘 `sort()` 方法一些鲜为人知但极其好用的操作技巧，或将为数据处理提供新的视角。

## `sort()` 方法基础

`Array.prototype.sort()` 方法默认情况下将数组元素转换为字符串，并根据字符串 Unicode 码位进行排序。其默认行为对于处理文本数据非常有用，但当解决方案需要超出普通字符串排序场景时，`sort()` 方法的真正威力得以显现。

## 默认排序行为的局限

```javascript
const numbers = [10, 5, 40, 25];
const defaultSortedNumbers = numbers.sort();
console.log(defaultSortedNumbers); // Output: [10, 25, 40, 5]
```

从以上代码可见，在处理非字符串数组时，若未提供比较函数，结果往往出人意料。这是由于默认排序的字符串比较机制造成的。因此，要充分利用 `sort()` 方法，必须深入其工作原理，掌握自定义比较函数的能力。

## 自定义比较函数的威力

自定义比较函数让开发者有机会定义排序逻辑，使其能够针对不同数据类型的数组执行有效排序。

### 数字数组的排序

```javascript
const numbers = [10, 5, 40, 25];
numbers.sort((a, b) => a - b);
```

在此代码片段中，一个简单的比较函数 `a - b` 能够确保数字按升序排序。发人深省的是，通过修改比较函数返回值的正负，能够轻易地切换排序的升降序。

### 对象数组的排序

```javascript
const items = [
  { name: "Banana", price: 150 },
  { name: "Apple", price: 100 },
  { name: "Orange", price: 120 }
];

items.sort((a, b) => a.price - b.price);
```

使用自定义比较函数，`sort()` 方法展现了对复杂数据结构处理的优雅。在此，通过返回对象 `price` 属性的差值，实现了基于价格的排序。

## `sort()` 使用技巧深掘

除了简单的升序或降序排序，`sort()` 方法的自定义比较函数还隐藏着更深层次的应用。

### 多关键字排序

处理现实世界的数据时，常需依据多个标准对数组进行排序。通过自定义比较函数，可以实现多关键字的排序。

```javascript
const products = [
  { category: "Electronics", name: "TV" },
  { category: "Furniture", name: "Desk" },
  { category: "Electronics", name: "Radio" }
];

products.sort((a, b) => {
  // First by category
  if (a.category < b.category) return -1;
  if (a.category > b.category) return 1;
  // Then by name
  if (a.name < b.name) return -1;
  if (a.name > b.name) return 1;
  return 0;
});
```

在这种情况下，数组元素首先按 `category` 排序，若 `category` 相同，则按 `name` 排序。

### 稳定性排序

自 JavaScript ES2019 规范起，`sort()` 方法被要求必须稳定。即排序前后，相等元素的顺序应当保持不变。这个改进为复杂排序提供了额外的保证，进一步加强了 `sort()` 方法的应用场景。

### 性能考量

`sort()` 方法在不同的 JavaScript 引擎中可能有不同的实现。大多数情况下，内部实现使得 `sort()` 在时间复杂度上非常高效。但在处理大规模数据集时，仍需注意性能影响，可能需要结合其他排序算法或策略进行优化。

## 结语

`Array.prototype.sort()` 是处理数组排序问题的一把利刃。通过精心设计的比较函数，可以为不同数据类型和复杂数据结构实现高效排序。掌握了 `sort()` 的高级用法，开发者能够优雅地处理日常编程中的排序任务，无论是简单的数字排序，还是复杂的多条件排序等。
