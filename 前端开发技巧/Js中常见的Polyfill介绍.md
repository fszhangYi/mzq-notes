在多姿多彩的JavaScript世界，Polyfill如同一座架在浏览器兼容性鸿沟之上的桥梁，让新生的Web特性得以在陈旧浏览器中生根发芽。本文将介绍常见的JavaScript Polyfill兼容方案，并举例说明它们的应用。

## Promise
旅程始于Promise，异步编程的瑰宝。在旧环境中缺失此宝物时，`es6-promise`库为其承担担保角色。

```javascript
// 引入es6-promise并实现polyfill
require('es6-promise').polyfill();
或者
const Promise = window.Promise = require('es6-promise').Promise;
```

## Array.prototype.includes
搜索Array中的元素变得如此简单。如果环境尚未支持，`array-includes`库即刻效力。

```javascript
// 引用array-includes库
var includes = require('array-includes');
includes.shim(); // 执行shim方法，将includes方法添加到Array.prototype

var arr = [ 'one', 'two' ];

includes(arr, 'one'); // true
includes(arr, 'three'); // false
includes(arr, 'one', 1); // false
```

## Object.assign
复制对象得心应手，但不在每处可行。`object-assign`便是解答。

```javascript
// 使用object-assign模块来模拟Object.assign
var assign = require('object-assign');

objectAssign({foo: 0}, {bar: 1});
//=> {foo: 0, bar: 1}
 
// multiple sources
objectAssign({foo: 0}, {bar: 1}, {baz: 2});
//=> {foo: 0, bar: 1, baz: 2}
 
// overwrites equal keys
objectAssign({foo: 0}, {foo: 1}, {foo: 2});
//=> {foo: 2}
 
// ignores null and undefined sources
objectAssign({foo: 0}, null, {bar: 1}, undefined);
//=> {foo: 0, bar: 1}

```

## Fetch
远程数据的擒取者，Fetch API。未得原生支持则启用`whatwg-fetch`。

```javascript
// 使用whatwg-fetch来引入fetch的polyfill
require('whatwg-fetch');
// 或者
import 'whatwg-fetch'

// 使用
fetch('/users.html')
  .then(function(response) {
    return response.text()
  }).then(function(body) {
    document.body.innerHTML = body
  })

```

## Array.prototype.find
寻觅数组中的宝物不再艰难，`array.prototype.find`库为探索者提供指南。

```javascript
// 引用array.prototype.find
require('array.prototype.find').shim();
```

## URLSearchParams
解析查询字符串，构建URL参数无须雕琢，`url-search-params-polyfill`轻松办到。

```javascript
// as a function
const find = require('array.prototype.find');
find([1, 2], function (x) { return x === 2; }); // 2

// to shim it
require('array.prototype.find').shim();

// Default:
[1, 5, 10, 15].find(function (a) { return a > 9; }) // 10
```

## String.prototype.startsWith
判断字符串始于何处？使用`string.prototype.startswith`，答案尽在掌握。

```javascript
// 添加String.prototype.startswith的Polyfill
require('string.prototype.startswith').shim();
```

## Array.from
将非数组之物变形为数组，“Array.from”应声而出，`array-from`便能施法。

```javascript
// 使用array-from模拟Array.from功能
const from = require('array.from');
const assert = require('assert');

assert.deepEqual(from('abc'), ['a', 'b', 'c']);
``` 

## Symbol
在现代编程语墓中绽放的Symbol，`es6-symbol`确保其于旧环境中同样灿烂。

```javascript
// 引入es6-symbol实现polyfill
const Symbol = require("es6-symbol");
 
const symbol = Symbol("My custom symbol");
const x = {};
 
x[symbol] = "foo";
console.log(x[symbol]);
("foo");
```

## Number.isNaN
确认NaN，无需多疑。`is-nan`提供确凿答案。

```javascript
// 使用is-nan代替Number.isNaN
Number.isNaN = require('number.isnan');
var assert = require('assert');

assert.notOk(Number.isNaN(undefined));
assert.notOk(Number.isNaN(null));
assert.notOk(Number.isNaN(false));
assert.notOk(Number.isNaN(true));
assert.notOk(Number.isNaN(0));
assert.notOk(Number.isNaN(42));
assert.notOk(Number.isNaN(Infinity));
assert.notOk(Number.isNaN(-Infinity));
assert.notOk(Number.isNaN('foo'));
assert.notOk(Number.isNaN(function () {}));
assert.notOk(Number.isNaN([]));
assert.notOk(Number.isNaN({}));

assert.ok(Number.isNaN(NaN));
```

## 结语

在兼容性的海洋中航行，Polyfill为船，开发者为帆。每一段代码都是原汁原味的探险，而Polyfill则确保旧世界与新世界之间的通行无阻。向着更宜居的互联网迈进，在每个角落激发代码的魅力，这是开发的艺术，这是进步的旋律。