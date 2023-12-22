# React组件性能优化
核心：减少渲染真实DOM的频率，以及减少VD比对的频率
## 1. 组件卸载前执行清理操作
- 注册的全局事件
- 定时器

证明：在组件挂载之后通过useEffect中开启定时器，销毁此组件之后，定时器还是存在的！

基于此，**需要在useEffect第一个形参函数的返回值中将定时器清除掉。**

## 2. 通过纯组件来提升性能
### 什么是纯组件？
所谓纯组件，就是当输入数据发生改变的时候，会将新数据和旧数据进行一次浅层比较，如果浅层比较结果相同，那么就不会引起重新渲染。

### 如何实现纯组件？
使用PureComponent类或者memo方法可以实现纯的类或者函数组件。

### 验证示例
```jsx
import React from "react";

export default class App extends React.Component {
  constructor() {
    super();
    this.state = { name: "张三" };
  }

  updateName() {
    setInterval(() => this.setState({ name: "张三" }), 1000);
  }

  componentDidMount() {
    this.updateName();
  }

  render() {
    return (
      <div>
        <RegularComponent name={this.state.name} />
        {/* 这里可能有其他组件或JSX元素 */}
      </div>
    );
  }
}
```

## 3. 在类组件中使用shouldComponentUpdate
由于使用PureComponent只能进行浅层的比较，所以在类组件中使用shouldComponentUpdate生命周期函数能够自定义用户的比较行为。

此生命周期函数的返回值是一个布尔值，如果为true表示需要更新，反之则不需要进行更新；此函数接受两个参数，其一是nextProps，另外一个nextState。分别表示外部和内部数据。

```jsx
import React from "react";

export default class App extends React.Component {
  constructor() {
    super();
    this.state = {
      person: {
        name: "张三",
        age: 20,
        job: "waiter"
      }
    };
  }

  componentDidMount() {
    setTimeout(() => {
      // 这里使用扩展运算符合并对象来确保我们创建了person对象的一个新副本
      this.setState({ person: { ...this.state.person, job: "chef" } });
    }, 2000);
  }

  shouldComponentUpdate(nextProps, nextState) {
    // 只在person对象的name或age属性发生变化时更新组件
    if (this.state.person.name !== nextState.person.name || this.state.person.age !== nextState.person.age) {
      return true;
    }
    return false;
  }

  render() {
    return (
      // 此处根据你的需求可以添加任何需要显示的内容
      <div>
        Name: {this.state.person.name},
        Age: {this.state.person.age},
        Job: {this.state.person.job}
      </div>
    );
  }
}

```

## 4. 通过函数式组件React.memo提升性能
父组件的渲染会引起子组件的渲染

证明示例：
```jsx
import React, { useState, useEffect } from 'react';

function App() {
  const [name] = useState("张三");
  const [index, setIndex] = useState(0);

  useEffect(() => {
    const intervalId = setInterval(() => {
      setIndex(prev => prev + 1);
    }, 1000);

    // 清除interval，防止内存泄漏
    return () => clearInterval(intervalId);
  }, []);

  return (
    <div>
      {index}
      <ShowName name={name} />
    </div>
  );
}

function ShowName({ name }) {
  console.log("rendering ShowName");
  return <div>{name}</div>;
}

export default App;
```

使用memo优化上述代码
```jsx
const ShowName = React.memo(function ({ name }) {
  console.log("rendering ShowName");
  return <div>{name}</div>;
})
```

memo具有第二个参数，第二个参数也是一个函数，此函数返回一个布尔值，其作用是自定义用户的比较行为；如果返回值是true则表示比较的两个对象（也是此函数的两个入参，分别为: prevProps和nextProps）是相同的，因此也就不需要重新渲染；否则则需要重新渲染。

```jsx
import React, { memo, useEffect, useState } from "react";

// 比较函数，用以优化渲染
function comparePerson(prevProps, nextProps) {
  if (
    prevProps.person.name !== nextProps.person.name ||
    prevProps.person.age !== nextProps.person.age
  ) {
    return false; // 如果person的name或age改变了，就重新渲染
  }
  return true; // 如果person的name或age没变，不重新渲染
}

// 用memo包裹的组件，将比较函数作为第二个参数传入
const ShowPerson = memo(function ShowPerson({ person }) {
  console.log("rendering ShowPerson");
  return (
    <div>
      {person.name} {person.age}
    </div>
  );
}, comparePerson);

function App() {
  const [person, setPerson] = useState({ name: "张三", age: 20, job: "waiter" });

  useEffect(() => {
    const intervalId = setInterval(() => {
      // 更新设置person状态对象中的job属性，而不是name或age
      setPerson(prevPerson => ({ ...prevPerson, job: "chef" }));
    }, 1000);

    // 清除interval，防止内存泄漏
    return () => clearInterval(intervalId);
  }, []);

  return (
    <div>
      <ShowPerson person={person} />
    </div>
  );
}

export default App;
```

## 5. 使用组件的懒加载来提升组件性能
使用懒加载的组件优化的核心逻辑在于减少bundle文件的大小，加快组件的呈现速度。但是，采用懒加载的组件会被打包到不同的文件中（分包）

### 5.1 路由组件懒加载
```jsx
import React, { lazy, Suspense } from 'react';
import { BrowserRouter, Link, Route, Switch } from "react-router-dom";

// 使用React的lazy函数动态导入组件
const Home = lazy(() => import(/* webpackChunkName: "Home" */ "./Home"));
const List = lazy(() => import(/* webpackChunkName: "List" */ "./List"));

function App() {
  return (
    <BrowserRouter>
      <Link to="/">Home</Link>
      <Link to="/list">List</Link>
      <Switch>
        // 使用Suspense包裹Route，并提供fallback来展示加载状态
        <Suspense fallback={<div>Loading...</div>}>
          <Route path="/" component={Home} exact />
          <Route path="/list" component={List} />
        </Suspense>
      </Switch>
    </BrowserRouter>
  );
}

export default App;
```

代码分析：

-   `lazy` 是一个React函数，它允许你定义一个动态加载的组件。这里，`Home`和`List`组件都是通过`lazy`函数和动态`import`进行定义的。Webpack将这些动态导入的组件分离到不同的代码块（chunks），当访问对应路由时才会加载它们。
-   `Suspense`组件是React内置的一个组件，它允许你在渲染等待内容（如懒加载组件）时显示一些回退内容。在这个例子中，回退内容是一个简单的`<div>Loading...</div>`，它会在懒加载组件被加载和渲染之前显示。
-   `BrowserRouter` 是`react-router-dom`库中的组件，它使用HTML5历史API（`pushState`, `replaceState`和`popstate`事件）来保持UI和URL的同步。
-   `Link`组件提供声明式的、可访问的导航的方式。
-   `Route`是配置路由的基本单元，它将一个路径和一个组件映射起来，当路径匹配时就会渲染对应的组件。
-   `Switch`组件用于渲染第一个匹配当前位置的`<Route>`或`<Redirect>`。

这种方式使得在应用启动时不会加载所有组件，而是仅在用户导航到相应的路由时才加载对应的组件，从而优化了性能。

### 5.2 根据某种条件进行组件懒加载
使用条件：组件不会随着条件频繁切换的场景下
```jsx
import React, { lazy, Suspense } from "react";

function App() {
  let LazyComponent = null;
  if (true) {
    LazyComponent = lazy(() => import(/* webpackChunkName: "Home" */ "./Home"));
  } else {
    LazyComponent = lazy(() => import(/* webpackChunkName: "List" */ "./List"));
  }

  return (
    <Suspense fallback={<div>Loading</div>}>
      <LazyComponent />
    </Suspense>
  );
}

export default App;
```

## 6. 使用Fragment避免额外标记
那就是：`<Fragment></Fragment>`

## 7. 避免使用内联函数提升函数性能
原因：render函数每次执行渲染的时候都会重新创建此内敛函数的实例，导致React在进行虚拟DOM的对比的时候，同一个位置的内敛函数并不相等，由此导致两个消耗：新的创建需要消耗；旧的回收也需要消耗。

不好的实践：
```jsx
onChange={e=>this.setState({value:e.target.value})}
```

好的实践：
```jsx
this.handleOnChange = e=>this.setState({value:e.target.value});
...
onChange={handleOnChange}
```

## 8. 正确的更正this的指向问题
修正this指向问题的方法有好几种，但是通过对比下来，最佳实践为：
```jsx
constructor(){
    super();
    this.handleClick = this.handleClick.bind(this);
}
```

不好的实践为：
```jsx
<Button onClick={this.handleClick.bind(this)}>按钮</Button>
```
原因：上述代码在render执行的时候都会执行一次，重复次数多，不像constructor只执行一次。

## 9. 避免在类组件中使用箭头函数创建类方法
在类组件中使用箭头函数的好处就是完全不用担心this的指向问题；因为箭头函数并不会改变this的指向。但是我看到过一句话：this的指向问题从来就不是使用箭头函数的原因。因此，并不推荐在类组件中使用箭头函数创建方法。

从功利的角度来看，如果使用箭头函数创建类的方法，此方法不会挂载在原型上，而是作为实例的一个属性。也就是说如果此类组件实例化很多次，那么此方法也会被实例化相同次数，这会造成极大的浪费。

因此，在类组件中解决this的最佳实践仍然是在构造函数中bind(this)

## 10. 避免使用内联样式属性
如果在项目中使用如下的代码，那么在编译之后，内敛的style会被映射成为js代码，最后就变成了js创建样式，导致浏览器会花费更多的时间执行脚本和渲染UI，从而降低了性能。

核心问题：CSS渲染UI的速度远超过js，因此能不用js操作样式就不要用！这一点很重要。本质上还是js操作DOM很费时间。

不好的实践：
```jsx
fucntion App () {
  return <div style={{backgroundColor: 'red'}}>div</div>
}
```

## 11. 对条件渲染进行优化
这点主要是针对：**频繁的挂载和卸载组件是一项非常消耗性能的事情** 这一事实提出的优化，其本质仍然是**尽量减少对DOM的操作**

好的实践
```jsx
function App() {
  return (
    <>
      {true && <AdminHeader />}
      <Header />
      <Content />
    </>
  );
}

```

不好的实践
```jsx
function App() {
  if (true) {
    return (
      <>
        <AdminHeader />
        <Header />
        <Content />
      </>
    );
  } else {
    return (
      <>
        <Header />
        <Content />
      </>
    );
  }
}

```

第一种做法中，随着条件的改变，重新渲染的只有AdminHeader组件，而第二种做法三个组件都会重新渲染，这也是由于虚拟DOM的对比策略所决定的。

## 12. 避免重复的无限渲染
避免在componentWillUpdate、componentDidUpdate或者render(是纯函数)方法中调用setState等可以触发组件在此渲染的做法。本质上是避免**render函数循环调用自身**。

## 13. 为组件创建错误边界
先说不足：错误边界本质上是一个组件；但是只能在同步错误发生的时候显示出来，异步错误是没有办法被错误边界响应的！

错误边界（Error Boundaries）是React的一个特性，它可以捕获其子组件树中JavaScript错误，记录这些错误，并显示备用UI，而不是让整个组件树崩溃。错误边界只能通过类组件来实现，因为需要使用生命周期方法`componentDidCatch`或`getDerivedStateFromError`。

以下分别介绍在类组件和函数式组件中如何处理错误，并举例说明。

### 类组件中的错误边界

在类组件中，你可以定义一个错误边界组件，如下所示：
```jsx
import React from 'react';

class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // 当子组件抛出异常，这里将会被调用，返回新的state
    return { hasError: true };
  }

  componentDidCatch(error, info) {
    // 你同样可以在这里记录错误信息
    console.error('ErrorBoundary caught an error', error, info);
  }

  render() {
    if (this.state.hasError) {
      // 当发生错误时，你可以渲染任何自定义的回退UI
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children;
  }
}

export default ErrorBoundary;

```
然后你可以像这样使用`ErrorBoundary`组件：
```jsx
<ErrorBoundary>
  <MyComponent />
</ErrorBoundary>

```

这样，如果`MyComponent`或者其任何子组件在渲染过程中发生JavaScript错误，`ErrorBoundary`就会显示备用UI，并防止整个应用崩溃。

### 函数式组件中的错误处理

函数式组件不能直接创建错误边界，因为它们不支持`componentDidCatch`或`getDerivedStateFromError`这类生命周期方法。然而，你可以在函数式组件内使用hooks来处理错误，例如使用`useState`和`useEffect`来模拟类似的行为。不过，这不是标准的错误边界实现，标准的错误边界目前只能通过类组件来实现。

但是，你可以通过将函数式组件包裹在上面定义的错误边界类组件中来提供错误捕获功能。

例如：
```jsx
function MyFunctionalComponent() {
  useEffect(() => {
    try {
      // 这里是可能会抛出错误的代码
    } catch (error) {
      // 你可以在这里处理错误，例如设置状态显示错误信息
    }
  });

  return (
    // 你的组件返回值
  );
}

// 应用错误边界
<ErrorBoundary>
  <MyFunctionalComponent />
</ErrorBoundary>
```
在这个例子中，任何在`MyFunctionalComponent`中发生的错误都需要自己处理，并不利用错误边界来捕获。但是被`ErrorBoundary`包裹的话，任何子组件树中的错误仍然可以被`ErrorBoundary`捕获。

总而言之，如果你希望在函数式组件中享有错误边界的保护，你需要将函数式组件放入一个可以作为错误边界的类组件之内。 直到React提供函数式组件的官方错误边界支持，这种方式将是常规的做法。

### 总结一下
本质上还是：条件渲染；只不过引发条件变化的**源**在于：是否发生了错误！

## 14. 避免数据结构的突变
结论：组件中的props和state的数据结构应该保持一致，数据结构的突变会导致输出不一致！！这一点在state的层数比较深的时候一定要引起额外注意！

```jsx
onClick={() =>
  this.setState({
    ...this.state,
    employee: {
      ...this.state.employee,
      age: 30
    }
  })
}
```

## 15. 优化依赖项大小
有一些库不支持动态加载，比如说lodash。但是lodash提供了一些插件，使用这些插件也能够实现按需加载相同的效果，从而显著的减少最终打包成的bundle的大小。
