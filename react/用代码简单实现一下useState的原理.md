# 面试官：用代码简单实现一下useState的原理

当我们在React函数组件中使用`useState`钩子时，我们通常只需要关心如何声明状态以及如何更新它。但是，要实现`useState`的背后原理，则需要深入了解状态是如何在函数组件的渲染周期中保持和更新的。本文将通过一段代码和相应的解释，来简单阐述`useState`钩子函数的实现思路。

首先，我们定义了三个全局变量：

```javascript
let state = []; // 用来保存状态的数组
let setters = []; // 用来保存设置状态函数的数组
let stateIndex = 0; // 表示当前状态索引的变量
```

这三个变量是整个`useState`实现的核心：

- `state`用于保存组件的所有状态。
- `setters`用于保存与每个状态相关联的更新函数。
- `stateIndex`用于跟踪当前正在处理的状态在`state`数组中的位置。

### 状态更新函数的生成

为了生成状态的更新函数，我们定义了一个`createSetter`函数：

```javascript
function createSetter(index) {
  return function(newState) {
    state[index] = newState;
    render();
  };
}
```

这个函数接受一个索引参数`index`，然后返回一个新的函数。当这个新函数被调用时（比如当状态需要更新时），它会更新对应索引`index`下的状态，并且通过调用`render`函数来触发组件的重新渲染。

### useState函数的实现

接下来，我们实现了模仿React的`useState`函数本身：

```javascript
function useState(initialState) {
  state[stateIndex] = state[stateIndex] ? state[stateIndex] : initialState;
  setters.push(createSetter(stateIndex));
  let setter = setters[stateIndex];
  let value = state[stateIndex];
  stateIndex++;
  return [value, setter];
}
```

在这个函数中，我们首先确认是否已经有现存的状态值；如果没有，我们就使用传入的`initialState`作为初始值。然后，我们对应地生成一个状态更新函数，并将其推入`setters`数组。在函数的最后，返回当前的状态值及其更新函数，并准备处理下一个状态，通过增加`stateIndex`。

### 组件的重新渲染

当状态更新函数被调用时，状态的值会更新，并触发组件的重新渲染。为此，我们定义了`render`函数：

```javascript
function render() {
  stateIndex = 0;
  ReactDOM.render(<App />, document.getElementById('root'));
}
```

在重新渲染前，我们首先将`stateIndex`重置回0，确保从第一个状态开始更新。然后，利用ReactDOM将更新后的`App`组件渲染至DOM。

### 使用自定义的useState

在App组件中，我们可以像使用React的`useState`那样使用我们自定义的`useState`：

```javascript
function App() {
  const [count, setCount] = useState(0);
  const [text, setText] = useState('hello');

  // ...
}
```

每次调用`useState`都会为对应的状态分配一个索引，并返回相应的状态和更新该状态的函数。通过解构赋值，我们可以方便地访问这些值与函数。

### 完整的示例

```javascript
import React from 'react';
import ReactDOM from 'react-dom';

// ...（前面定义的全局变量和函数）

function App() {
  // 使用自定义的useState钩子
  const [count, setCount] = useState(0);
  const [text, setText] = useState('hello');

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Count: {count}</button>
      <input value={text} onChange={e => setText(e.target.value)} />
    </div>
  );
}

export default App;
```

整个过程可以概括为：通过闭包和全局变量来跟踪和更新状态，同时在状态改变时触发组件的重新渲染。这是一种简单的方式去理解和模拟React的`useState`钩子，虽然实际React的实现会更复杂，同时包括了对组件生命周期和性能优化的考虑。