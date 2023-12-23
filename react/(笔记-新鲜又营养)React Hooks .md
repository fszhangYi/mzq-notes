基调：对于reacthooks的学习可以从三个方面展开：
- react hooks介绍
- react hooks使用
- react hooks封装
- react hooks原理

react hooks的本质作用是为了对react的函数式组件进行增强的，让函数式组件具有了存储状态的功能，同时具备了处理副作用（指发送网络请求或者进行DOM操作）的能力。或者说，让函数式组件拥有了部分类组件的功能。

### 类组件的不同和hook的作用
- 缺少逻辑复用的机制，只能通过HOC等复杂代码实现相同的逻辑复用，这会导致代码嵌套层数深导致代码臃肿，难以调试。
- 类组件难以维护，体现在将一组相干的业务逻辑拆分到不同的生命周期函数中去，这也造成了在同一个生命周期函数中维护了多个不相干的业务逻辑代码。 -- **而在函数式组件中引入的useEffect钩子函数则完美的解决了这个问题**
- 类组件中还具有特殊的this的指向问题，而保证this的指向问题需要花费更多的代码。这同时会造成代码更加复杂，难以维护。

以上三点，其实就是使用react hooks的原因所在了。

### react hooks定义以及常见的hooks
react hooks本质上是一堆钩子函数，通过这些钩子函数完成了对函数式组件的增强，内置的钩子有：
- useState // 使用闭包完成对状态的保存
- useEffects
- useReducer
- useRef
- useCallback
- useContext
- useMemo

### 1. useState
这段代码是一个React组件的实现，使用了React Hooks中的useState。以下是代码的内容：

```jsx
import React, { useState } from 'react';

function App () {
  const [count, setCount] = useState(0);
  return (
    <div>
      <span>{count}</span>
      <button onClick={() => setCount(count + 1)}>+1</button>
    </div>
  );
}
```

此代码定义了一个名为`App`的函数组件，该组件内部有一个状态`count`，它是通过`useState`钩子初始化为0的。组件返回一个`div`包含一个显示计数的`span`和一个按钮。当按钮被点击时，按钮的`onClick`事件处理函数会调用`setCount`以将`count`的值增加1。

useState的使用特点：
- 接受唯一的任意类型的参数作为初始值
- 返回值为数组，数组的第二个元素以set开头
- 此方法可以被调用多次
- **参数可以是一个函数，函数的执行结果会被作为初始值，并且此函数只会执行一次，这个特点很容易被忽略掉！在初始值为动态值的时候非常好用！**

针对第四点，有一个代码举例：
- 不好的实践
```jsx
const propsCount = props.count || 0;
const [ count, setCount ] = useState(propsCount);
```

- 好的实践
```jsx
const [ count, setCount ] = useState(()=> (props.count || 0));
```

关于useState的使用，还有两点注意：
- 设置状态值方法的参数可以是一个值也可以是一个函数
- 设置状态值方法的方法本身是异步的

举个例子，下面的两种做法都是可以的：
```jsx
setCount(count+1);

setCount(count=>count+1)
```

关于上面的第二点的验证可以使用改变document.title的方式：
```jsx

```

改成同步的可以写成：
```jsx
setCount(count=>(doucment.title = count+1, count+1))
```

### 2. useReducer
useReducer钩子函数的作用是为了让函数式组件保存状态，也就是说提供了另一种保存状态的方式。那么useReducer相对于useState的优点在于什么呢？使用useReducer可以对同一个数据进行多个既定Type类型的操作。

```jsx
import React, {useReducer} from 'react';

function App (){
    function reducer(state, action){
        switch(action.type){
            case 'increment':
                return state + 1;
            break;
            case 'decrement':
                return state - 1;
            break;
        }
    }
    
    const [count, dispatch] = useReducer(reducer, 0);
    
    return (
        <div>
            <button onClick={()=>dispatch({type:'decrement'})}>-1</button>
            <span>{count}</span>
            <button onClick={()=>dispatch({type:'increment'})}>+1</button>
        </div>
    )
}

export default App;
```

### 3. useContext--跨组件层级获取数据的时候简化获取数据的代码
即外层组件中的数据不必通过透传的方式逐层传递到深层的子组件中去，子组件通过其他渠道也能够获取外层组件中的数据。
```jsx
import react, {createContext} from 'react';
const countContext = createContext();
function App () {
    return <countContext.Provider value={100} ><Foo /></countContext.Provider>
}

function Foo () {
    return <countContext.Consumer>{value=><div>{value}</div>}</countContext.Consumer>
}
```

或者，不想使用Consumer组件的话，可以写成：
```jsx
function Foo () {
    const value = useContext(countContext);
    return <div>{value}</div>
}
```

### 4. useEffect -- 让函数式组件具有处理副作用的功能，类似于生命周期函数
- 1. useEffect 执行时机

可以把 useEffect 看做 `componentDidMount`, `componentDidUpdate` 和 `componentWillUnmount `这三个函数的组合。
```jsx
useEffect(() => {})           // => componentDidMount, componentDidUpdate
useEffect(() => {}, [])       // => componentDidMount
useEffect(() => () => {})     // => componentWillUnmount
```

举一个简单的例子：
```jsx
import React, { useEffect } from "react";

function App() {
  function onScroll() {
    console.log('页面发生滚动了');
  }

  useEffect(() => {
    window.addEventListener('scroll', onScroll);
    return () => {
      window.removeEventListener('scroll', onScroll);
    };
  }, []);

  return <div>App works</div>;
}

export default App;

```

一个隐晦的点：
```jsx
const [count, setCount] = useState(0);

useEffect(() => {
  const timerId = setInterval(() => {
    setCount(() => count + 1);
  }, 1000);

  return () => {
    clearInterval(timerId);
  };
}, []);

```
上述代码无法完成累加效果，原因在于：
- `useEffect`的依赖数组`[]`是空的，所以这个`useEffect`只会在组件的挂载时运行一次。这意味着，计时器设置的时候，`count`状态的引用值将始终是初次渲染时的状态，即`0`。结果是，`setCount(() => count + 1);`这行代码每次执行时，都是将`0`加`1`，而不是累加。
因此需要修改为下面的形式：
```jsx
const [count, setCount] = useState(0);

useEffect(() => {
  const timerId = setInterval(() => {
    setCount(count + 1);
  }, 1000);

  return () => {
    clearInterval(timerId);
  };
}, [count]); // 不推荐

```

或者，
```jsx
useEffect(() => {
  const timerId = setInterval(() => {
    setCount(prevCount => prevCount + 1);
  }, 1000);

  return () => {
    clearInterval(timerId);
  };
}, []);

```

### 5. useEffect和异步函数
需要注意的一点就是useEffect的入参函数的返回值只能是一个function，因此不可以将此入参函数变成异步函数，也就是说，下面的写法是错误的！
```jsx
useEffect(async () => {}, [])
```

正确的做法应该为：
```jsx
useEffect(()=>{
    (async () => {
        await axios.get()
    })()
},[])
```

### 6. useMemo -- 计算属性
机制为：监听某个数据是否发生了变化，如果发生了变化就根据变化之后的值重新计算新值，这有利于避免昂贵的重复计算。
```jsx
import {useMemo} form 'react';

const result = useMemo(()=>{
    let _a;
    // compute result basing with result
    return _a;
}, [count])
```

### 7. memo方法 -- 注意它和useMemo没有什么关系
机制：性能优化，如果本组件中的数据没有发生变化，就会阻止其更新，类似于类组件中的`PureComponent`和`shouldComponentUpdate`

其基本的形式可以为：
```jsx
import React, { memo } from 'react';

function Counter () {
    return <div></div>;
}

export default memo(Counter);
```

通过一个场景说明memo的作用：

假设Counter组件作为了App组件的子组件，那么如果export default出去的是Counter而不是memo(Counter)，那么随着App组件的刷新，Counter组件会无条件的刷新；但是App刷新，Counter就要刷新这个事实虽然是React组件更新的机制但是很多情况下是不必要的，特别是Counter组件中的数据并没有发生更新的时候，因此，子啊Counter组件导出的时候在其前面加上memo()这个壳就可以实现App刷新的时候Counter不会更新，这样就可以提高一些性能了。

多说一句，此时Counter组件的刷新可以从两个途径实现：1. Counter内部状态改变，比如说其内部的setState的调用； 2. <Counter data={data} />即调用的时候的props数据发生了变化也会导致Counter的刷新。

总结一下，memo防止的实际上是“被动刷新”，而不是数据驱动的刷新。

### 8. useCallback -- 缓存函数，重新渲染的时候能够获取相同的函数实例
这里必须要澄清一下，为什么需要保证相同的函数示例。实际上，在js中，创建一个函数的消耗是非常小的，基本上可以忽略不计。所以使用useCallback保证组件在渲染前后其中的函数实例的一致性并不是使用useCallback的考量。

真正需要用到useCallback的地方在于：如果父组件中创建的函数示例cb需要传递给子组件，那么对于子组件来说，从props对象中接受的此属性将会引起子组件的重新渲染。即便是子组件使用memo包裹也是没有用的，这不是memo所解决的被动渲染的问题，而是传递到子组件的入参发生变化（一定会）引起子组件的刷新。

出于这样的考量，在将父组件中的函数传递给子组件的时候，使用useCallback保证此传递函数不会每次都随着父组件的更新而重新序列化，可以在很大程度上保证子组件避免没有必要的重新渲染。
```jsx
import React, { useCallback } from 'react';

function Counter() {
  const [count, setCount] = useState(0);
  const resetCount = useCallback(() => setCount(0), [setCount]);
  
  return <div>
    <span>{count}</span>
    <button onClick={() => setCount(count + 1)}>+1</button>
    <Test resetCount={resetCount}/>
  </div>
}

```

### 9. useRef -- 用来操作DOM的利器或保存数据
useRef保存的数据和useState保存的数据的机制是不同的。使用useState保存的数据在发生变化的时候会引起组件的重新渲染，而使用useRef保存的数据在发生变化的时候不会引起变化。这一点也可以反过来理解，使用useRef保存的数据是跨组件渲染的，也就是说组件的渲染不会引起useRef中的数据。

那么useRef通常存储的都是一些什么样的数据呢？一般来说通过useRef保存一些辅助数据是比较合适的。比如说用来保存定时器的返回值就非常的合适。

不使用useRef的时候无法完成清除定时器的任务：
```jsx
function App() {
  const [count, setCount] = useState(0);
  let timerId = null;

  useEffect(() => {
    timerId = setInterval(() => {
      setCount(count => count + 1);
    }, 1000)
  }, [])

  const stopCount = () => {
    clearInterval(timerId)
  }

  return <div>
    {count}
    <button onClick={stopCount}>停止</button>
  </div>;
}

export default App;

```

使用useRef之后就能够成功的清除定时器了:
```jsx
function App() {
  const [count, setCount] = useState(0);
  let timerId = React.useRef();

  useEffect(() => {
    timerId.current = setInterval(() => {
      setCount(count => count + 1);
    }, 1000)
  }, [])

  const stopCount = () => {
    clearInterval(timerId.current)
  }

  return <div>
    {count}
    <button onClick={stopCount}>停止</button>
  </div>;
}

export default App;

```

## 自定义hooks
为什么需要自定义hooks?
- 使用hook的形式封装和共享逻辑是标准模式

自定义hooks的本质
- 其本质就是自定义的逻辑和内置hooks的有机结合

形式上的要求
- 和自定义hooks相同，自定义的hooks也要求以use开头

步骤
- 1. 完成业务需求
- 2. 抽取公共部分到公共hooks库然后引入

### 1. 封装一个获取文章数据的hook
step1:
```jsx
import axios from 'axios';

function App() {
  const [post, setPost] = useState({});
  useEffect(() => {
    axios.get('https://jsonplaceholder.typicode.com/posts/1')
      .then(response => setPost(response.data));
  }, [])

  return <div>
    <div>{post.title}</div>
    <div>{post.body}</div>
  </div>;
}

export default App;

```

step2:
```jsx
import React, { useState, useEffect } from 'react';
import axios from 'axios';

function useGetPost() {
  const [post, setPost] = useState({});
  useEffect(() => {
    axios.get('https://jsonplaceholder.typicode.com/posts/1')
      .then(response => setPost(response.data));
  }, []);

  return [post, setPost];
}

function App() {
  const [post, setPost] = useGetPost();

  return <div>
    <div>{post.title}</div>
    <div>{post.body}</div>
  </div>;
}

export default App;

```

### 2. 封装一个表单提交的hook
```jsx
import React, { useState } from 'react';

function useUpdateInput(initialValue) {
  const [value, setValue] = useState(initialValue);
  
  const onChange = event => setValue(event.target.value);
  
  return {
    value,
    onChange
  };
}

function App() {
  const usernameInput = useUpdateInput('');
  const passwordInput = useUpdateInput('');
  
  const submitForm = event => {
    event.preventDefault();
    console.log(usernameInput.value);
    console.log(passwordInput.value);
  };
  
  return (
    <form onSubmit={submitForm}>
      <input type="text" name="username" {...usernameInput} />
      <input type="password" name="password" {...passwordInput} />
      <input type="submit" />
    </form>
  );
}

export default App;

```

虽然封装的思路很巧妙，但是不得不说在很多情况下都是忘了`event.preventDefault()`的。

## 路由相关的hooks
所谓的路由 hooks ，其实指的就是react-router-dom中提供的一些hooks：`useHistory useLocation useRouterMatch useParams`, 一共是四个。

路由导航的设置：
```jsx
import React from "react";
import { Link, Route } from "react-router-dom";
import Home from "./pages/Home";
import List from "./pages/List";

function App() {
  return (
    <>
      <div>
        <Link to="/home/zhangsan">首页</Link>
        <Link to="/list">列表页</Link>
      </div>
      <div>
        <Route path="/home/:name" component={Home} />
        <Route path="/list" component={List} />
      </div>
    </>
  );
}

export default App;

```

在路由子组件中使用这四个钩子函数：
```jsx
import React from "react";
import { useHistory, useLocation, useRouteMatch, useParams } from "react-router-dom";

export default function Home(props) {
  console.log(props);
  console.log(useHistory());
  console.log(useLocation());
  console.log(useRouteMatch());
  console.log(useParams());
  
  return <div>Home Works</div>;
}

```

## hooks的原理
### 1. 实现useState钩子函数的原理
其原理大概可以表示成为：
```jsx
import React from 'react';
import ReactDOM from 'react-dom';

let state = []; // 用来保存状态的数组
let setters = []; // 用来保存设置状态函数的数组
let stateIndex = 0; // 表示当前状态索引的变量

function createSetter(index) {
  return function(newState) {
    state[index] = newState;
    render();
  };
}

function useState(initialState) {
  state[stateIndex] = state[stateIndex] ? state[stateIndex] : initialState;
  setters.push(createSetter(stateIndex));
  let setter = setters[stateIndex];
  let value = state[stateIndex];
  stateIndex++; // 遍历到下一个状态
  return [value, setter];
}

function render() {
  stateIndex = 0; // 重置索引，这样可以保证在重新渲染时从第一个状态开始
  ReactDOM.render(<App />, document.getElementById('root')); // 渲染组件
}

// App组件示例，可以在这里使用我们自定义的useState钩子
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
可以看出来，useState的本质是通过闭包实现的！
### 2. 实现useEffect钩子函数的原理
useEffect的作用原理大概可以通过下面的代码简要说明：
```jsx
import React from 'react';
import ReactDOM from 'react-dom';

let prevDepsAry = []; // 存放依赖项数组的上一次值
let effectIndex = 0; // 当前副作用索引

function useEffect(callback, depsAry) {
  // 检查callback是否为函数
  if (Object.prototype.toString.call(callback) !== '[object Function]') {
    throw new Error('useEffect的第一个参数必须是一个函数');
  }

  // 如果没有依赖项数组，则每次渲染都调用callback
  if (typeof depsAry === 'undefined') {
    callback();
  } else {
    // 检查depsAry是否为数组
    if (Object.prototype.toString.call(depsAry) !== '[object Array]') {
      throw new Error('useEffect的第二个参数必须是数组');
    }

    // 获取上一次的依赖项数组值
    let prevDeps = prevDepsAry[effectIndex];

    // 判断依赖项数组是否发生了变化
    let hasChanged = prevDeps ? !depsAry.every((dep, index) => dep === prevDeps[index]) : true;

    // 如果依赖项发生变化，调用callback
    if (hasChanged) {
      callback();
    }

    // 存储当前的依赖项数组值，供下一次渲染时使用
    prevDepsAry[effectIndex] = depsAry;
  }

  // 增加索引，以供下一个副作用使用
  effectIndex++;
}

function render() {
  // 渲染函数开始时，重置副作用索引
  effectIndex = 0;
  // 渲染应用
  ReactDOM.render(<App />, document.getElementById('root'));
}

// App组件示例，这里可以使用自定义的useState和useEffect
function App() {
  // 试验性地模拟一些hooks
  // ...

  useEffect(() => {
    console.log('副作用函数执行了');
    // 这里可以添加一些副作用逻辑，例如API请求，订阅事件等

    // 有依赖项的情况下，只有在依赖项发生变化时才执行
  }, [/* 依赖项数组 */]);

  return (
    // 组件内容
    <div></div>
  );
}

render(); // 首次渲染
```
### 3. 实现useReducer钩子函数的原理
useReducer钩子函数本质上实际是对useState的setState部分的增强：
```jsx
function useReducer = (reducer, initialValue) => {
    const [state, setState] = useState(initialValue);
    function dispatch (action) {
        const newValue = reducer(state, action);
        setState(newValue);
    }
    return [state, dispatch];
}
```

## 总结
useState和useEffect钩子函数的实现原理本质上是在合适的时机调用`ReactDOM.render`这个函数进行更新。而useReducer钩子函数的本质是对useState钩子函数的增强。