本文介绍mobx6在react项目中的4种常见的操作，它们分别是：
- 如何创建RootStore
- 如何使用RootStore
- 如何创建子Store
- 组件中如何使用子Store


## 功能一：创建RootStore
在React应用程序中创建一个集中化的`RootStore`是有效管理状态的关键步骤，尤其是在使用MobX进行状态管理时。`RootStore`模式允许我们为所有的store创建一个单一的引用点，使它们在整个应用程序中易于访问。让我们逐步了解如何实现这一点，并查看相应的代码。

### 第1步：引入createContext和useContext

我们首先从React中引入`createContext`和`useContext`。`createContext`用来创建一个新的上下文对象——一个React结构，它使组件能够共享一些全局数据，而不必手动通过组件树逐级传递props。`useContext`钩子让我们能够在函数组件中访问上下文数据。

```jsx
import { createContext, useContext } from "react";
```

### 第2步：引入子Store

接着，我们引入两个子Store：`CounterStore`和`TodoListStore`。这些store包含应用程序不同部分的状态和逻辑。例如，`CounterStore`可能管理计数器的状态，而`TodoListStore`可能管理待办事项列表的状态。

```jsx
import CounterStore from "./Counter/CounterStore";
import TodoListStore from "./Todos/TodoListStore";
```

### 第3步：创建RootStore类

接下来，我们创建一个`RootStore`类。该类的构造函数实例化了子store，并将它们作为类的属性分配。这样，我们可以在一个地方管理我们应用程序的所有状态，并且通过根据需要添加更多子store来轻松扩展。

```jsx
class RootStore {
  constructor() {
    this.counterStore = new CounterStore();
    this.todoListStore = new TodoListStore();
  }
}
```

### 第4步：使用createContext创建一个Provider

我们使用`createContext`来创建一个`RootStoreContext`。然后，我们定义一个`RootStoreProvider`组件。这个组件使用`RootStoreContext.Provider`来包裹其子组件，为它们提供`store`属性的值，这应该是`RootStore`的一个实例。这使得我们的`RootStore`可以被应用中任何使用`useRootStore`钩子的组件访问。

```jsx
const RootStoreContext = createContext();

const RootStoreProvider = ({ store, children }) => {
  return (
    <RootStoreContext.Provider value={store}>
      {children}
    </RootStoreContext.Provider>
  );
};
```

### 第5步：创建一个自定义钩子来访问Provider的值

我们创建了一个名为`useRootStore`的自定义钩子，它让我们可以轻松地从任何函数组件中访问我们的`RootStore`。通过使用`RootStoreContext`调用`useContext`，我们可以获取提供给上下文提供者的值，即我们的`RootStore`实例。

```jsx
const useRootStore = () => {
  return useContext(RootStoreContext);
};
```

### 第6步：导出RootStore类，Provider和钩子

最后，我们导出`RootStore`类、`RootStoreProvider`组件和`useRootStore`钩子，以便它们可以在应用程序的其他部分被导入和使用。

```jsx
export { RootStore, RootStoreProvider, useRootStore };
```

通过遵循这些步骤，我们已经建立了一个可扩展的状态管理结构，该结构利用了React的上下文系统。以下是从上述步骤中整理出的完整代码：

```jsx
// 第1步：引入createContext, useContext
import { createContext, useContext } from "react";
// 第2步：引入子Store
import CounterStore from "./Counter/CounterStore";
import TodoListStore from "./Todos/TodoListStore";

// 第3步：创建RootStore类，将实例属性链接到子Store
class RootStore {
  constructor() {
    this.counterStore = new CounterStore();
    this.todoListStore = new TodoListStore();
  }
}

// 第4步：使用createContext

创建一个Provider
const RootStoreContext = createContext();

const RootStoreProvider = ({ store, children }) => {
  return (
    <RootStoreContext.Provider value={store}>
      {children}
    </RootStoreContext.Provider>
  );
};

// 第5步：创建一个自定义钩子来获取Provider中的value
const useRootStore = () => {
  return useContext(RootStoreContext);
};

// 第6步：将RootStore类，Provider和钩子导出
export { RootStore, RootStoreProvider, useRootStore };
```

Creating a centralized `RootStore` is an essential step in managing state effectively in a React application, especially when using MobX for state management. The `RootStore` pattern allows us to create a single point of reference for all our stores, making them easily accessible throughout our application. Let's break down the steps and the corresponding code to understand how this is achieved.

### Step 1: Import createContext and useContext

We start by importing `createContext` and `useContext` from React. `createContext` is used to create a new Context object — a React construct that enables components to share some global data without having to pass props down manually through the component tree. `useContext` is the hook that allows us to tap into the context data within our functional components.

```jsx
import { createContext, useContext } from "react";
```

### Step 2: Import Sub-Stores

We then import our sub-stores, which in this case are `CounterStore` and `TodoListStore`. These stores contain the state and logic for different parts of our application. `CounterStore` might manage a counter's state, while `TodoListStore` might manage the state of a list of todo items.

```jsx
import CounterStore from "./Counter/CounterStore";
import TodoListStore from "./Todos/TodoListStore";
```

### Step 3: Create the RootStore Class

Next, we create a `RootStore` class. The constructor of this class instantiates the sub-stores and assigns them as properties of the class. This way, we can have all our application's state managed in one place, and it can be easily extended by adding more sub-stores as needed.

```jsx
class RootStore {
  constructor() {
    this.counterStore = new CounterStore();
    this.todoListStore = new TodoListStore();
  }
}
```

### Step 4: Use createContext to Create a Provider

We use `createContext` to create a `RootStoreContext`. Then, we define a `RootStoreProvider` component. This component uses the `RootStoreContext.Provider` to wrap its children, providing them with the value of the `store` prop, which should be an instance of `RootStore`. This makes our `RootStore` accessible to any component in our app using the `useRootStore` hook.

```jsx
const RootStoreContext = createContext();

const RootStoreProvider = ({ store, children }) => {
  return (
    <RootStoreContext.Provider value={store}>
      {children}
    </RootStoreContext.Provider>
  );
};
```

### Step 5: Create a Custom Hook to Access the Provider's Value

We create a custom hook called `useRootStore`, which allows us to easily access our `RootStore` from any functional component. By calling `useContext` with `RootStoreContext`, we can get the value provided to the context's provider, which is our `RootStore` instance.

```jsx
const useRootStore = () => {
  return useContext(RootStoreContext);
};
```

### Step 6: Export the RootStore Class, Provider, and Hook

Finally, we export the `RootStore` class, the `RootStoreProvider` component, and the `useRootStore` hook, so they can be imported and used in other parts of our application.

```jsx
export { RootStore, RootStoreProvider, useRootStore };
```

By following these steps, we have set up a scalable state management structure that leverages React's context system. Below is the complete code assembled from the steps above:

```jsx
// 1. Import createContext, useContext
import { createContext, useContext } from "react";
// 2. Import sub-stores
import CounterStore from "./Counter/CounterStore";
import TodoListStore from "./Todos/TodoListStore";

// 3. Create RootStore class that links instance properties to sub-stores
class RootStore {
  constructor() {
    this.counterStore = new CounterStore();
    this.todoListStore = new TodoListStore();
  }
}

// 4. Create a Provider using createContext
const RootStoreContext = createContext();

const RootStoreProvider = ({ store, children }) => {
  return (
    <RootStoreContext.Provider value={store}>
      {children}
    </RootStoreContext.Provider>
  );
};

// 5. Create a custom hook to access the value in the Provider
const useRootStore = () => {
  return useContext(RootStoreContext);
};

// 6. Export the RootStore class, Provider, and hook
export { RootStore, RootStoreProvider, useRootStore };
```

---
---
---
## 功能二：使用RootStore
在React应用中使用`RootStore`模式来管理状态，我们需要通过一系列的步骤来集成和使用`RootStore`。以下是对这些步骤的详细说明：

### 第1步：获取RootStore和Provider

首先，我们需要从我们的`stores`目录导入`RootStore`和`RootStoreProvider`。这是我们刚刚创建的上下文和提供者，它们将允许我们在整个应用中访问和提供状态。

```jsx
import { RootStore, RootStoreProvider } from "./stores/RootStore";
```

### 第2步：引入被包裹的Children

接下来，我们导入需要状态管理的组件，这里以`Counter`组件和`TodoListView`组件为例。这些组件将能够通过`RootStore`访问应用程序状态。

```jsx
import Counter from "./components/Counter/Counter";
import TodoListView from "./components/Todos/TodoListView";
```

### 第3步：创建RootStore的实例化对象

在应用的最顶层，我们创建`RootStore`的一个实例。这个实例将包含所有子store的实例，从而形成我们状态管理的核心。

```jsx
const rootStore = new RootStore();
```

### 第4步：将实例化对象作为Provider的store然后包裹Children从而形成视图根组件

在我们的根组件`App`中，我们使用`RootStoreProvider`组件并将`rootStore`实例作为其`store`属性传递。这样一来，`RootStoreProvider`就会将`rootStore`实例提供给所有子组件，这些子组件通过context API访问到状态。

```jsx
function App() {
  return (
    <RootStoreProvider store={rootStore}>
      <TodoListView />
      <Counter />
    </RootStoreProvider>
  );
}
```

这样一来，我们的`App`组件就成为了视图的根组件，其中包含了所有需要状态的子组件，并且这些子组件都可以通过`RootStore`访问应用程序的状态。我们也确保了`RootStore`的状态在组件间是共享的。

导出`App`组件作为应用的主入口点。

```jsx
export default App;
```

通过上述步骤，我们建立了一个能够在应用各层间共享和管理状态的结构。下面是完整的代码：

```jsx
/**
 * 使用RootStore的步骤：
 */

// 第1步：获取RootStore和Provider
import { RootStore, RootStoreProvider } from "./stores/RootStore";
// 第2步：引入被包裹的Children
import Counter from "./components/Counter/Counter";
import TodoListView from "./components/Todos/TodoListView";

// 第3步：创建RootStore的实例化对象
const rootStore = new RootStore();

// 第4步：将实例化对象作为Provider的store然后包裹Children从而形成视图根组件
function App() {
  return (
    <RootStoreProvider store={rootStore}>
      <TodoListView />
      <Counter />
    </RootStoreProvider>
  );
}

export default App;
```

In a React application, utilizing the `RootStore` pattern for state management requires a series of steps to integrate and utilize the `RootStore`. Here's a detailed explanation of these steps along with the corresponding code:

### Step 1: Retrieve RootStore and Provider

Firstly, we need to import `RootStore` and `RootStoreProvider` from our `stores` directory. These are the context and provider we just created, which will allow us to access and provide state across our entire application.

```jsx
import { RootStore, RootStoreProvider } from "./stores/RootStore";
```

### Step 2: Import Wrapped Children

Next, we import the components that will require state management, exemplified here by the `Counter` and `TodoListView` components. These components will be able to access the application state through the `RootStore`.

```jsx
import Counter from "./components/Counter/Counter";
import TodoListView from "./components/Todos/TodoListView";
```

### Step 3: Instantiate RootStore

At the top level of our application, we create an instance of `RootStore`. This instance will contain instances of all sub-stores, forming the core of our state management.

```jsx
const rootStore = new RootStore();
```

### Step 4: Wrap Children with Provider Using the RootStore Instance

In our root component `App`, we use the `RootStoreProvider` component and pass the `rootStore` instance as its `store` prop. This way, the `RootStoreProvider` will provide the `rootStore` instance to all child components, which access the state through the context API.

```jsx
function App() {
  return (
    <RootStoreProvider store={rootStore}>
      <TodoListView />
      <Counter />
    </RootStoreProvider>
  );
}
```

By doing this, our `App` component becomes the root of the view component tree, containing all the child components that require state, and these children can access the application's state through the `RootStore`. We also ensure that the `RootStore`'s state is shared among the components.

Export the `App` component as the main entry point of the application.

```jsx
export default App;
```

Through the steps above, we've established a structure that can share and manage state across different layers of the application. Below is the complete code assembled from the steps:

```jsx
/**
 * Steps to use RootStore:
 */

// Step 1: Retrieve RootStore and Provider
import { RootStore, RootStoreProvider } from "./stores/RootStore";
// Step 2: Import Wrapped Children
import Counter from "./components/Counter/Counter";
import TodoListView from "./components/Todos/TodoListView";

// Step 3: Instantiate RootStore
const rootStore = new RootStore();

// Step 4: Wrap Children with Provider Using the RootStore Instance to Form the Root View Component
function App() {
  return (
    <RootStoreProvider store={rootStore}>
      <TodoListView />
      <Counter />
    </RootStoreProvider>
  );
}

export default App;
```

---
---
---
## 功能三：创建store的步骤
在React应用中使用MobX进行状态管理时，我们需要按照一定的步骤创建和设置可观察状态、行为和计算值。以下是对提供的代码片段的解释，详细说明了创建MobX存储的步骤：

### 第1步：创建以'Store'结尾的类

我们首先定义一个类，这个类将作为我们的存储。通常使用以'Store'结尾的命名约定，以表明其在应用程序架构中的作用。

```javascript
class TodoListStore {
  //...
}
```

### 第2步：在构造函数前初始化状态和初始值

在构造函数之前，我们用初始值初始化状态。这里，`todos`是一个将包含我们待办事项的数组，`filter`是一个将决定显示哪些待办事项的字符串。

```javascript
todos = [];
filter = "all";
```

### 第3步：设置构造函数

在构造函数内部，我们可以注入外部引用，发起网络请求，并使用`makeObservable`来注解实例属性和方法。

```javascript
constructor(todos) {
  if (todos) this.todos = todos;
  makeObservable(this, {
    // 注解...
  });
  this.loadTodos();
}
```

### 第4步：用MobX装饰器注解状态和方法

我们使用`observable`标记MobX应该跟踪的状态，用`action`表示将更改状态的方法，用`computed`标记基于状态的派生属性，用`action.bound`强制在行动中执行`this`上下文。

```javascript
makeObservable(this, {
  todos: observable,
  createTodo: action,
  removeTodo: action,
  unCompletedTodoCount: computed,
  filter: observable,
  changeFilter: action,
  filterTodos: computed
});
```

### 第5步：计算方法作为Getter

计算方法本质上是getter，当状态改变时重新计算它们的值。

```javascript
get unCompletedTodoCount() {
  //...
}
get filterTodos() {
  //...
}
```

### 第6步：行动用于修改状态

标注为`action`的方法主要用于修改状态。

```javascript
changeFilter(filter) {
  //...
}
createTodo(title) {
  //...
}
removeTodo(id) {
  //...
}
```

### 第7步：在未注解的方法中使用runInAction修改状态

对于未注解的方法，我们在`runInAction`内包装状态修改，以确保它们被MobX视为行动，这对于异步操作很重要。

```javascript
async loadTodos() {
  //...
  runInAction(() => {
    //...
  });
}
```

### 第8步：暴露存储类

最后，我们暴露整个类。我们导出的本质上是创建`TodoListStore`实例的蓝图。

```javascript
export default TodoListStore
```

以下是按照上述解释整理的完整代码：

```javascript
/**
 * 创建一个Mobx的Store的步骤
 */
import TodoStore from "./TodoStore"
import {
  action,
  computed,
  makeObservable,
  observable,
  runInAction
} from "mobx"

import axios from "axios"

class TodoListStore {
  todos = []
  filter = "all"

  constructor(todos) {
    if (todos) this.todos = todos;
    makeObservable(this, {
      todos: observable,
      createTodo: action,
      removeTodo: action,
      unCompletedTodoCount: computed,
      filter: observable,
      changeFilter: action,
      filterTodos: computed
    });
    this.loadTodos();
  }

  get unCompletedTodoCount() {
    return this.todos.filter(todo => !todo.completed).length;
  }

  get filterTodos() {
    switch (this.filter) {
      case "all":
        return this.todos;
      case "active":
        return this.todos.filter(todo => !todo.completed);
      case "completed":
        return this.todos.filter(todo => todo.completed);
      default:
        return this.todos;
    }
  }

  changeFilter(filter) {
    this.filter = filter;
  }

  createTodo(title) {
    this.todos.push(new TodoStore(title));
  }

  removeTodo(id) {
   

 const index = this.todos.findIndex(todo => todo.id === id);
    this.todos.splice(index, 1);
  }

  async loadTodos() {
    let todos = await axios
      .get("http://localhost:3005/todos")
      .then(response => response.data);
    runInAction(() => {
      todos.forEach(todo => {
        this.todos.push(new TodoStore(todo.title));
      });
    });
  }
}

export default TodoListStore
```

Creating a MobX store involves a systematic approach to setting up observable state, actions, and computed values. Here's an explanation of the code snippet provided, detailing the steps for creating a MobX store:

### Step 1: Create a Class Ending with 'Store'

We start by defining a class that will serve as our store. The naming convention typically ends with 'Store' to signify its purpose in the application architecture.

```javascript
class TodoListStore {
  //...
}
```

### Step 2: Initialize State Before the Constructor

Before the constructor, we initialize the state with its initial values. Here, `todos` is an array that will hold our todo items, and `filter` is a string that will determine which todos to display.

```javascript
todos = [];
filter = "all";
```

### Step 3: Set Up Constructor Function

Inside the constructor function, we can inject external references, make network requests, and annotate instance properties and methods using `makeObservable`.

```javascript
constructor(todos) {
  if (todos) this.todos = todos;
  makeObservable(this, {
    // Annotations...
  });
  this.loadTodos();
}
```

### Step 4: Annotate State and Methods with MobX Decorators

We use `observable` to mark the state that MobX should track, `action` to denote methods that will change the state, `computed` for derived properties based on the state, and `action.bound` to enforce the `this` context inside actions.

```javascript
makeObservable(this, {
  todos: observable,
  createTodo: action,
  removeTodo: action,
  unCompletedTodoCount: computed,
  filter: observable,
  changeFilter: action,
  filterTodos: computed
});
```

### Step 5: Computed Methods Act as Getters

Computed methods are essentially getters that recalculate their value when the state changes.

```javascript
get unCompletedTodoCount() {
  //...
}
get filterTodos() {
  //...
}
```

### Step 6: Actions for Modifying State

Methods annotated as `action` are primarily used to modify the state.

```javascript
changeFilter(filter) {
  //...
}
createTodo(title) {
  //...
}
removeTodo(id) {
  //...
}
```

### Step 7: Modify State Inside Unannotated Methods Using runInAction

For methods not annotated, we wrap state modifications inside `runInAction` to ensure they are treated as actions by MobX, which is important for asynchronous operations.

```javascript
async loadTodos() {
  //...
  runInAction(() => {
    //...
  });
}
```

### Step 8: Expose the Store Class

Finally, we expose the entire class. What we're exporting is essentially a blueprint for creating a `TodoListStore` instance.

```javascript
export default TodoListStore
```

Here is the complete code after the explanation:

```javascript
import TodoStore from "./TodoStore"
import {
  action,
  computed,
  makeObservable,
  observable,
  runInAction
} from "mobx"

import axios from "axios"

class TodoListStore {
  todos = []
  filter = "all"

  constructor(todos) {
    if (todos) this.todos = todos;
    makeObservable(this, {
      todos: observable,
      createTodo: action,
      removeTodo: action,
      unCompletedTodoCount: computed,
      filter: observable,
      changeFilter: action,
      filterTodos: computed
    });
    this.loadTodos();
  }

  get unCompletedTodoCount() {
    return this.todos.filter(todo => !todo.completed).length;
  }

  get filterTodos() {
    switch (this.filter) {
      case "all":
        return this.todos;
      case "active":
        return this.todos.filter(todo => !todo.completed);
      case "completed":
        return this.todos.filter(todo => todo.completed);
      default:
        return this.todos;
    }
  }

  changeFilter(filter) {
    this.filter = filter;
  }

  createTodo(title) {
    this.todos.push(new TodoStore(title));
  }

  removeTodo(id) {
    const index = this.todos.findIndex(todo => todo.id === id);
    this.todos.splice(index, 1);
  }

  async loadTodos() {
    let todos = await axios
      .get("http://localhost:3005/todos")
      .then(response => response.data);
    runInAction(() => {
      todos.forEach(todo => {
        this.todos.push(new TodoStore(todo.title));
      });
    });
  }
}

export default TodoListStore
```

---
---
---
## 功能四：组件中使用mobx的store

在React组件中使用MobX 6来管理状态涉及一系列步骤，旨在确保组件能够响应状态的变化。下面是对提供的代码片段的解释，详细说明了如何在组件中使用MobX 6：

### 第1步：引入获取Provider的store的钩子函数

我们首先从`RootStore`模块中导入`useRootStore`钩子函数。这个钩子允许我们在组件中访问通过`RootStore`提供的状态。

```javascript
import { useRootStore } from "../../stores/RootStore";
```

### 第2步：引入observer

为了使组件能够响应MobX状态的变化而更新，我们引入`observer`函数。这是一个高阶组件，它包装我们的React组件，确保当观察到的状态发生变化时，组件能够重新渲染。

```javascript
import { observer } from "mobx-react-lite";
```

### 第3步：从Provider的store中解构出子Store

在组件中，我们使用`useRootStore`钩子函数来获取`RootStore`实例，并从中解构出所需的子store，例如`todoListStore`。

```javascript
const { todoListStore } = useRootStore();
```

### 第4步：使用子Store上的方法更新state

在组件的UI部分，我们使用从子store中获取的方法来更新状态。在这个例子中，我们使用`removeTodo`方法来从todo列表中删除一个项目。

```javascript
<button
  onClick={() => todoListStore.removeTodo(todo.id)}
  className="destroy"
/>
```

### 第5步：返回被observer包裹的组件

最后，我们需要确保返回的组件被`observer`函数包裹。这确保了组件能够观察到状态变化，并且在状态更新时重新渲染。

```javascript
export default observer(TodoView);
```

通过以上步骤，我们确保了React组件能够有效地利用MobX 6进行状态管理，同时响应状态的变化。以下是根据上述解释整理的完整代码：

```javascript
/**
 * 组件中使用mobx6的步骤：
 */

import { useRootStore } from "../../stores/RootStore";
import { observer } from "mobx-react-lite";

function TodoView({ todo }) {
  const { todoListStore } = useRootStore();

  return (
    <li className={todo.completed ? "completed" : ""}>
      <div className="view">
        <input
          checked={todo.checked}
          onChange={todo.toggle}
          className="toggle"
          type="checkbox"
        />
        <label>{todo.title}</label>
        <button
          onClick={() => todoListStore.removeTodo(todo.id)}
          className="destroy"
        />
      </div>
      <input className="edit" />
    </li>
  );
}

export default observer(TodoView);
```

In React components, using MobX 6 for state management involves a series of steps, aimed at ensuring that the component can respond to changes in the state. Here's an explanation of the provided code snippet, detailing how to use MobX 6 in a component:

### Step 1: Import the Hook to Retrieve the Store from the Provider

First, we import the `useRootStore` hook from the `RootStore` module. This hook allows us to access the state provided by the `RootStore` in our component.

```javascript
import { useRootStore } from "../../stores/RootStore";
```

### Step 2: Import observer

To enable the component to update in response to changes in MobX's state, we import the `observer` function. This is a higher-order component that wraps our React component, ensuring that it re-renders when the observed state changes.

```javascript
import { observer } from "mobx-react-lite";
```

### Step 3: Destructure the Sub-Store from the Provider's Store

Inside the component, we use the `useRootStore` hook to get the `RootStore` instance and then destructure the required sub-store, such as `todoListStore`.

```javascript
const { todoListStore } = useRootStore();
```

### Step 4: Use Methods from the Sub-Store to Update the State

In the UI part of the component, we use methods obtained from the sub-store to update the state. In this example, we use the `removeTodo` method to delete an item from the todo list.

```javascript
<button
  onClick={() => todoListStore.removeTodo(todo.id)}
  className="destroy"
/>
```

### Step 5: Return the Component Wrapped by observer

Finally, we need to ensure that the returned component is wrapped by the `observer` function. This ensures that the component observes changes in the state and re-renders when the state is updated.

```javascript
export default observer(TodoView);
```

By following these steps, we ensure that the React component can effectively use MobX 6 for state management and respond to changes in the state. Here is the complete code based on the explanation above:

```javascript
/**
 * Steps for using MobX 6 in a component:
 */

import { useRootStore } from "../../stores/RootStore";
import { observer } from "mobx-react-lite";

function TodoView({ todo }) {
  const { todoListStore } = useRootStore();

  return (
    <li className={todo.completed ? "completed" : ""}>
      <div className="view">
        <input
          checked={todo.checked}
          onChange={todo.toggle}
          className="toggle"
          type="checkbox"
        />
        <label>{todo.title}</label>
        <button
          onClick={() => todoListStore.removeTodo(todo.id)}
          className="destroy"
        />
      </div>
      <input className="edit" />
    </li>
  );
}

export default observer(TodoView);
```