在React的丰富多彩的生态圈中，Hooks的问世无疑是划时代的进步。它们为函数组件注入了状态管理和副作用处理的能力，未经多余修饰、无须变换身形，优雅且直接。本文将介绍React中的常用Hooks，并提供精选示例代码，展现其在日常开发中的应用魔力。

## 基础Hooks

### `useState`

`useState`用于为函数组件添加状态。

```javascript
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>您点击了 {count} 次</p>
      <button onClick={() => setCount(count + 1)}>
        点击我
      </button>
    </div>
  );
}
```

### `useEffect`

`useEffect`用来在函数组件中执行副作用操作。

```javascript
import React, { useState, useEffect } from 'react';

function UserStatus({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetchUser(userId).then(setUser);
  }, [userId]); // 仅当 userId 更改时，才重新获取用户数据

  // ...
}
```

### `useContext`

`useContext`允许组件订阅React的Context变更。

```javascript
import React, { useContext } from 'react';
import { ThemeContext } from './ThemeContext';

function ThemedButton() {
  const theme = useContext(ThemeContext);
  
  return (
    <button style={{ background: theme.background, color: theme.foreground }}>
      我被主题化了
    </button>
  );
}
```

## 额外Hooks

### `useReducer`

`useReducer`是`useState`的替代品，适用于复杂的状态逻辑。

```javascript
import React, { useReducer } from 'react';

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, { count: 0 });

  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
    </>
  );
}
```

### `useCallback`

`useCallback`返回一个记忆化的回调函数。

```javascript
import React, { useState, useCallback } from 'react';

function Todos({ addTodo }) {
  const [text, setText] = useState('');

  const onChange = useCallback(e => {
    setText(e.target.value);
  }, []);

  const onSubmit = useCallback(
    e => {
      e.preventDefault();
      addTodo(text);
      setText('');
    },
    [text, addTodo],
  );

  return (
    <form onSubmit={onSubmit}>
      <input value={text} onChange={onChange} />
      <button type="submit">添加</button>
    </form>
  );
}
```

### `useMemo`

`useMemo`返回一个记忆化的值。

```javascript
import React, { useMemo } from 'react';

function computeExpensiveValue(a, b) {
  // 假设这是一个计算代价昂贵的函数
}

function MyComponent({ a, b }) {
  const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
  return <>{memoizedValue}</>;
}
```

### `useRef`

`useRef`返回一个可变的ref对象，其`.current`属性被初始化为传入的参数。

```javascript
import React, { useRef } from 'react';

function TextInputWithFocusButton() {
  const inputEl = useRef(null);

  const onButtonClick = () => {
    // 当按钮点击时，input元素将获得焦点
    inputEl.current.focus();
  };

  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>聚焦到输入框</button>
    </>
  );
}
```

### `useImperativeHandle`

`useImperativeHandle`自定义`ref`暴露给父组件的实例值。

```javascript
import React, { useRef, useImperativeHandle, forwardRef } from 'react';

const FancyInput = forwardRef((props, ref) => {
  const inputRef = useRef();
  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    }
  }));

  return <input ref={inputRef} />;
});

function Parent() {
  const inputRef = useRef();
  
  return (
    <>
      <FancyInput ref={inputRef} />
      <button onClick={() => inputRef.current.focus()}>
        聚焦到输入框
      </button>
    </>
  );
}
```

### `useLayoutEffect`

`useLayoutEffect`的 signature 与`useEffect`相同，但它在所有DOM变化后同步触发。

```javascript
import React, { useLayoutEffect, useRef } from 'react';

function MeasureExample() {
  const divRef = useRef();
  useLayoutEffect(() => {
    console.log(divRef.current.getBoundingClientRect().height);
  });

  return <div ref={divRef}>Hello, world</div>;
}
```

### `useDebugValue`

`useDebugValue`可以在React开发者工具中显示自定义的hook标签。

```javascript
import React, { useDebugValue, useState } from 'react';

function useCustomHook() {
  const [value, setValue] = useState(null);
  
  useDebugValue(value ?? 'loading...');
  
  // ...
}

function Component() {
  useCustomHook();
  // ...
}
```

React Hooks为开发者提供了一系列编码工具，极大简化了状态与生命周期功能的实现，其内在的精妙机制彰显了React团队对开发便捷性的深刻理解。