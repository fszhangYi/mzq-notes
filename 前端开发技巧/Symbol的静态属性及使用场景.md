# Js Symbol的静态属性及用途

JavaScript 语言在 ES6 规范中引入了 `Symbol` 类型，它是一种原始数据类型，用于创建唯一的标识符。`Symbol` 对象是不可改变且唯一的，适合用作对象属性的键。除了作为对象的属性键之外，`Symbol` 还有许多静态属性（也称为“内置符号”或“众所周知的符号”），这些属性包含了与语言内部机制相关联的特殊符号值。本文将介绍 `Symbol` 类型的所有静态属性，并举例说明它们的用途和使用场景。

## Symbol.hasInstance

`Symbol.hasInstance` 属性用来定制 `instanceof` 运算符，在一个构造器对象上使用 `instanceof` 时会调用这个方法。

```javascript
class MyClass {
    static [Symbol.hasInstance](instance) {
        return Array.isArray(instance);
    }
}
console.log([] instanceof MyClass); // true
```

在这个例子中，`MyClass` 用自定义的 `Symbol.hasInstance` 函数覆盖了默认行为，检查对象是否为数组，从而使得数组实例在 `instanceof` 运算时返回 `true`。

## Symbol.isConcatSpreadable

`Symbol.isConcatSpreadable` 属性是一个布尔值，表示当在 `Array.prototype.concat()` 时，对象是否可以展开。

```javascript
let collection = {
    0: 'hello',
    1: 'world',
    length: 2,
    [Symbol.isConcatSpreadable]: true
};
console.log(['Hi'].concat(collection)); // ['Hi', 'hello', 'world']
```

在这个例子中，`collection` 对象通过设置 `Symbol.isConcatSpreadable` 为 `true`，可以被 `concat` 方法展开并合并到结果数组中。

## Symbol.iterator

`Symbol.iterator` 属性指向对象的默认迭代器方法，通常用于定义对象的 `for-of` 循环行为。

```javascript
let fibonacci = {
  [Symbol.iterator]() {
    let pre = 0, cur = 1;
    return {
      next() {
        [pre, cur] = [cur, pre + cur];
        return { done: false, value: cur };
      }
    };
  }
}
for (let n of fibonacci) {
  if (n > 1000) break;
  console.log(n);
}
```

上述代码中定义了斐波那契序列的迭代器，可以通过 `for-of` 循环输出序列中的数。

## Symbol.match

`Symbol.match` 属性用于定义匹配的正则表达式行为，即当一个对象被 `String.prototype.match()` 方法调用时所执行的操作。

```javascript
let hasNumber = {
    [Symbol.match](string) {
        return /\d+/.test(string);
    }
};
console.log('123'.match(hasNumber)); // true
```

在这个例子中，`hasNumber` 定义了一个自定义的 `match` 行为，当字符串中包含数字时返回 `true`。

## Symbol.replace

`Symbol.replace` 属性定义了当一个对象被 `String.prototype.replace()` 方法调用时如何替换字符串中的匹配部分。

```javascript
let replaceHyphens = {
    [Symbol.replace](string, replacer) {
        return string.replace(/-/g, replacer);
    }
};
console.log('123-45-678'.replace(replaceHyphens, ':')); // '123:45:678'
```

这个例子中，`replaceHyphens` 将字符串中的所有连字符替换为冒号。

## Symbol.search

`Symbol.search` 属性定义了当 `String.prototype.search()` 方法被调用时，如何返回字符串中匹配项的索引。

```javascript
let searchObject = {
    [Symbol.search](string) {
        return string.indexOf('JavaScript');
    }
};
console.log('Hello JavaScript!'.search(searchObject)); // 6
```

在这个例子中，`searchObject` 实现了搜索 "JavaScript" 字符串并返回它在源字符串中的位置。

## Symbol.species

`Symbol.species` 属性用于创建派生对象时确定默认的构造函数。

```javascript
class MyArray extends Array {
  static get [Symbol.species]() { return Array; }
}
let a = new MyArray(1,2,3);
let mapped = a.map(x => x * x);
console.log(mapped instanceof MyArray); // false
console.log(mapped instanceof Array);   // true
```

这个例子表明，`MyArray` 创建的新数组 `mapped` 不是 `MyArray` 的实例，它是 `Array` 的实例。

## Symbol.split

`Symbol.split` 属性定义当一个对象被 `String.prototype.split()` 方法调用时，如何分割字符串。

```javascript
let splitByLength = {
    [Symbol.split](string) {
        return string.length <= 3 ? [string] : [string.slice(0, 3), string.slice(3)];
    }
};
console.log('JavaScript'.split(splitByLength)); // ['Jav', 'aScript']
```

在这个例子中，`splitByLength` 对象根据长度进行分割，将字符串分为长度不超过3的部分。

## Symbol.toPrimitive

`Symbol.toPrimitive` 属性用于指定对象转换为相应的原始值时的行为。

```javascript
let obj = {
    [Symbol.toPrimitive](hint) {
        if (hint === 'number') {
            return 42;
        }
        if (hint === 'string') {
            return 'fourty two';
        }
        return true;
    }
};
console.log(+obj);  // 42
console.log(`${obj}`); // 'fourty two'
```

在这个例子中，根据转换提示（hint），`obj` 对象会返回不同的原始值。

## Symbol.toStringTag

`Symbol.toStringTag` 属性用于自定义对象的默认字符串描述，是在对象上调用 `toString` 方法时使用的。

```javascript
let myCollection = {
    get [Symbol.toStringTag]() {
        return 'MyCollection';
    }
};
console.log(myCollection.toString()); // [object MyCollection]
```

`myCollection` 对象的 `toString` 方法返回了自定义的字符串描述 `[object MyCollection]`。

## Symbol.unscopables

`Symbol.unscopables` 属性指出了哪些属性名会被 `with` 环境绑定排除。

```javascript
Array.prototype[Symbol.unscopables] = Object.assign({}, Array.prototype[Symbol.unscopables], {
    map: true
});
with ([]) {
    console.log(map); // ReferenceError: map is not defined
}
```

这个例子通过把 `Array.prototype.map` 方法标记为 `unscopables`，在 `with` 语句中无法直接使用 `map` 方法。

综上所述，由于 `Symbol` 类型的不可变和唯一性质，它的内置符号作为语言的元操作部分，扮演了调整对象默认行为的关键角色。无论是改变内置操作的工作方式，还是定义新的行为，`Symbol` 的静态属性为自定义高级抽象提供了强大的支持，极大地增强了语言的灵活性和扩展性。在日益复杂的应用开发中，了解并合理应用 `Symbol` 的静态属性，有助于提高代码的可维护性和扩展性。