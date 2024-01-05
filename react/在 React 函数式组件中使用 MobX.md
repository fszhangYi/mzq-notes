🌟 [MobX](https://mobx.js.org/README.html) 是一个简洁的状态管理库📚，它通过透明的函数响应式编程（TFRP）🔮 使得状态管理变得简单和可扩展。在 React 函数式组件中使用 MobX 可以让我们更轻松地管理组件状态✨。这篇文章将介绍如何在 React 函数式组件中结合使用 MobX 和 `mobx-react` 包提供的 `observer` 函数👀 以及 `Provider`🏭 和 `useContext`🔗。

## 安装🛠️

首先，需要确保 `mobx` 和 `mobx-react` 已经添加到项目依赖中。

```shell
npm install mobx mobx-react 📦
```

## 定义你的 Store 🗃️

创建一个 store 来存储应用状态📈。

```javascript
import { makeAutoObservable } from 'mobx';

class TodoStore {
  todos = [];

  constructor() {
    makeAutoObservable(this);
  }

  addTodo(todo) {
    this.todos.push(todo);
  }

  get todoCount() {
    return this.todos.length;
  }
}

export const todoStore = new TodoStore();
```

## 使用 `Provider` 和 `useContext` 提供和使用 Store🔄

通过 `Provider` 可以将 store 传递到 React 组件树中🌲。然后，使用 React 的 `useContext` 钩子函数将 store 注入到函数式组件中。

创建一个 context：

```javascript
import { createContext } from 'react';
import { todoStore } from './TodoStore';

export const StoreContext = createContext(todoStore);
```

使用 `Provider` 将 store 提供给组件：

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import { StoreContext } from './store';
import App from './App';
import { todoStore } from './TodoStore';

ReactDOM.render(
  <StoreContext.Provider value={todoStore}>
    <App />
  </StoreContext.Provider>,
  document.getElementById('root')
);
```

## 在函数式组件中使用 Store🛠️

在函数式组件中，可以通过 `useContext` 钩子和 `observer` 函数来使用 store 和响应其变化🌀。

```javascript
import React, { useState, useContext } from 'react';
import { observer } from 'mobx-react';
import { StoreContext } from './store';

const TodoList = observer(() => {
  const store = useContext(StoreContext);
  const [newTodo, setNewTodo] = useState('');

  const handleInputChange = (e) => {
    setNewTodo(e.target.value);
  };

  const handleFormSubmit = (e) => {
    e.preventDefault();
    store.addTodo(newTodo);
    setNewTodo('');
  };

  return (
    <div>
      <form onSubmit={handleFormSubmit}>
        <input
          type="text"
          value={newTodo}
          onChange={handleInputChange}
        />
        <button type="submit">Add Todo</button>
      </form>
      <ul>
        {store.todos.map((todo, index) => (
          <li key={index}>{todo}</li>
        ))}
      </ul>
      <div>Total Todos: {store.todoCount}</div>
    </div>
  );
});

export default TodoList;
```

在上述代码中，`TodoList` 组件通过 `useContext` 获取到 `todoStore`🛍️，通过 `observer` 函数包裹组件以确保组件能响应状态变化🔄。每当状态更新时（如添加了一个 todo）✏️，`TodoList` 组件会重新渲染以反映最新的状态🔄。

通过使用 MobX、`Provider`🏭 和 React 的 Context API🔗，我们可以在函数式组件中便利地管理和使用状态✨。