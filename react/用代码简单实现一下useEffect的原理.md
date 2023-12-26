# 面试官：用代码简单实现一下useEffect的原理

React的`useEffect`钩子是React函数组件中处理副作用(例如API请求、订阅或手动修改DOM等)的重要工具。在本文中，我将通过一个简单的例子解释如何用代码实现`useEffect`的基本原理。

首先，定义了两个全局变量，用于跟踪不同的副作用状态：

```javascript
let prevDepsAry = []; // 存放依赖项数组的上一次值
let effectIndex = 0; // 当前副作用索引
```

`prevDepsAry`用于记录上次渲染时的依赖项数组，`effectIndex`用来标示当前处理的副作用的位置。

接下来是`useEffect`函数的实现：

```javascript
function useEffect(callback, depsAry) {
  // 校验callback是否为函数
  if (Object.prototype.toString.call(callback) !== '[object Function]') {
    throw new Error('useEffect的第一个参数必须是一个函数');
  }

  // 不提供依赖项数组的情况下，默认每次渲染都执行callback
  if (typeof depsAry === 'undefined') {
    callback();
  } else {
    // 校验depsAry是否为数组
    if (Object.prototype.toString.call(depsAry) !== '[object Array]') {
      throw new Error('useEffect的第二个参数必须是数组');
    }

    // 获取依赖项数组的前一个值
    let prevDeps = prevDepsAry[effectIndex];

    // 比较依赖项数组的每一个值，确定是否发生变化
    let hasChanged = prevDeps ? !depsAry.every((dep, index) => dep === prevDeps[index]) : true;

    // 如果依赖项变化或者是首次渲染(hasChanged为true)，则执行callback
    if (hasChanged) {
      callback();
    }

    // 将当前的依赖项数组存储起来，用于下次渲染时比较
    prevDepsAry[effectIndex] = depsAry;
  }

  // 增加副作用索引，准备下一个副作用
  effectIndex++;
}
```

`useEffect`的实现逻辑为：

1. 验证传入的`callback`是否为函数。如果不是，抛出错误。
2. 如果没有传入`depsAry`，那么每次组件渲染时都执行`callback`。
3. 如果传入了`depsAry`，首先验证其为数组。然后，获取上一次的依赖数组并与当前数组逐项比较。如果存在差异或者是首次渲染，则执行`callback`。
4. 更新`prevDepsAry`的对应项，在下次组件渲染时用作比较。
5. `effectIndex`自增，确保下一个`useEffect`的处理使用正确的索引。

下一步定义了`render`函数：

```javascript
function render() {
  // 重设副作用索引
  effectIndex = 0;
  // 使用ReactDOM来渲染App组件
  ReactDOM.render(<App />, document.getElementById('root'));
}
```

在每次渲染前，`effectIndex`必须重置为0，这保证了`useEffect`在处理依赖项时的正确性。

最后，在App组件中使用`useEffect`：

```javascript
function App() {
  // 使用自定义的useState和useEffect

  useEffect(() => {
    console.log('副作用函数执行了');
    // 在这里可能会执行如API请求、订阅事件等具有副作用的操作
    // 当依赖项发生变化时，这里的代码会被执行
  }, [/* 依赖项数组 */]);

  return (
    // 组件的JSX结构
    <div></div>
  );
}
```

在组件中通过调用`useEffect`，并传递一个执行副作用操作的函数以及依赖项数组，实现了依赖项变化时执行副作用逻辑的需求。

最后一步，调用`render()`以触发首次渲染。

通过上述代码，我们简单地回答了如何实现`useEffect`的问题。当然，实际的React`useEffect`实现更加复杂，并且涉及到调度和清理操作，但上面的代码为理解其基本原理提供了良好的起点。