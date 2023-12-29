## 前言

事情是这样的，前几个月我写了一篇文章[每日前端手写题--day4](https://juejin.cn/post/7283516197487869971#comment)，在其中讨论了如何在js中对数组进行扁平化处理。然后就有个大佬提供了两种巧妙的解决方案（见方法五和方法六）。我大为震撼，因此将目前我推荐的数组扁平化方法整理出来，供各位大佬参考。

## 正文
在JavaScript的日常使用中，处理多层嵌套数组是一项常见任务。阅读下文，探究几种将多维数组转换为一维数组的方法，每种方法都有其独特之处。

### 方法一：forEach 和 push

数组扁平化的根本思路是将多维数组展开为一维数组。第一种方法采用经典的递归思想：遍历数组中的每个元素，判断是否为数组。如果不是数组，则将元素push到结果数组中; 如果是数组，则对该数组元素进行递归处理。代码示例如下：

```javascript
function _flat(targetArray, container = []) {
    if (!Array.isArray(targetArray)) return container;
    
    targetArray.forEach(item => {
        if (!Array.isArray(item)) {
            container.push(item);
        } else {
            _flat(item, container);
        }
    });
    
    return container;
}

const rst = _flat([[[[[[1],2],3],4],5,6],7]);
console.log('rst: ', rst);
```

### 方法二: Array.prototype.flat

近年来，ES6新增了`Array.prototype.flat`方法，旨在简化扁平化操作。对于该方法，理解其工作原理意义重大。它默认只会拆解一层嵌套数组。通过循环调用直到无法展开为止，我们可以得到完全扁平化的数组。

```javascript
function _flat2(targetArray) {
    if (!Array.isArray(targetArray)) return [];
    
    let _loop = targetArray;
    while (true) {
        const beforeFlat = _loop.length;
        const _Arr = _loop.flat();
        const afterFlat = _Arr.length;

        if (beforeFlat === afterFlat) return _Arr;
        _loop = _Arr;
    }
}

const rst2 = _flat2([[[[[[1],2],3],4],5,6],7]);
console.log('rst2: ', rst2);
```

### 方法三: findIndex 和 splice

第三种方法利用了`Array.prototype.findIndex`以及`Array.prototype.splice`。首先找到数组中第一个还未展开的数组元素，然后使用`splice`将其展开。这种方法会更改原数组。

```javascript
function _flat3(targetArray) {
    if (!Array.isArray(targetArray)) return [];
    
    while (true) {
        const arrItemIndex = targetArray.findIndex(item => Array.isArray(item));
        if (arrItemIndex === -1) return targetArray;
        
        targetArray.splice(arrItemIndex, 1, ...targetArray[arrItemIndex]);
    }
}

const rst3 = _flat3([[[[[[1],2],3],4],5,6],7]);
console.log('rst3: ', rst3);
```

### 方法四: stack

使用栈的数据结构可以仿佛过程中的遍历。具体操作是：将源数组整体入栈，然后逐一出栈，检查是否为数组。若是数组则展开后继续入栈; 若不是则入另一个栈存储结果。这种方法本质上与递归相同，但使用栈可以降低操作复杂度。

```javascript
function _flat4(targetArray) {
    if (!Array.isArray(targetArray)) return [];
    
    const a = [...targetArray];
    const b = [];
    while (a.length) {
        const _tmp = a.pop();
        if (Array.isArray(_tmp)) {
            a.push(..._tmp);
        } else {
            b.push(_tmp);
        }
    }
    
    return b;
}

const rst4 = _flat4([[[[[[1],2],3],4],5,6],7]);
console.log('rst4: ', rst4);
```

### 方法五: toString 和 split

这种思路利用了数组的`toString`方法，该方法会将数组转换为由逗号分隔的字符串，然后使用`split`方法得到结果数组。

```javascript
const arr = [1, [2, 3], 4, [[5]]];
const rst = arr.toString().split(',').map(item => +item);
```
`toString()`方法的一个有趣特性是，它可以将多层嵌套的数组转换成一个由逗号分隔的扁平化字符串。在字符串形态下，数组中各元素之间的嵌套结构信息丢失，仅保留了元素值。例如，一个像`[1, [2, [3, [4]]]]`的数组，通过`toString()`方法处理后，就会变成`"1,2,3,4"`。这正是我们期望的一维形态，只不过是以字符串的形式存在。

但这还不是完整的解决办法。字符串虽然扁平化了，但数组还未形成。这时`split(',')`方法派上了用场。它根据逗号分隔符将字符串再次转换为数组，由于原始的嵌套结构已经被`toString()`方法抹除，结果数组就是一个完全扁平的数组。最后，为了确保数组中的元素类型正确（因为`split()`会将每个元素当作字符串），可以使用`map()`方法将每个字符串元素转换成其原始类型。

```javascript
const arr = [1, [2, 3], 4, [[5]]];
const rst = arr.toString().split(',').map(item => +item);
```

在上面的代码中，`+item`是一个快速的技巧，用于将字符串转换为数字。

### 方法六: JSON.stringify

`JSON.stringify`和`JSON.parse`是一对强大的方法，可以用来序列化和解析数据。在JavaScript中，这对方法经常被用来进行深拷贝操作，但它们同样可以用来进行数组的扁平化。

其核心思想是：首先使用`JSON.stringify`将多维数组转换为字符串形式，同时保持了数组元素之间的逗号分隔。这时，嵌套数组被转换成了括号和逗号的组合。接下来，通过正则表达式`.replace(/$|$/g, "")`移除字符串中所有的中括号`[]`，剩下的就只有逗号以及数字。最后，通过`JSON.parse`将处理后的字符串重新构造成JavaScript数组。

```javascript
const arr = [1, [2, 3], 4, [[5]]];
const res = JSON.stringify(arr).replace(/$|$/g, "");
const _a = JSON.parse("[" + res + "]");
```

这种方法的妙处在于使用了`JSON`对象的序列化和解析能力，从而简化了扁平化操作。需要注意的是，由于`JSON.stringify`会将数组中的所有内容（包括数字、字符串、布尔值及`null`）序列化为字符串，所以在使用这种方法时，应保证数组内部不含有除上述类型之外的元素（比如函数或循环引用），因为这些无法通过`JSON.stringify`正确序列化。

在掌握了这些方法后，便可以根据具体情况选择合适的扁平化方法。每种方法都有其适用场景和性能考量。掌握这些技巧，你就可以更加自如地处理JavaScript中的数组扁平化问题。
