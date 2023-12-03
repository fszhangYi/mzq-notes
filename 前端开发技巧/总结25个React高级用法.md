使用了近两年的React框架，最近将开发过程中使用到的一些“高级”用法整理出来，一方面梳理一下自己的思路；另一方面期待能够帮助有需要的同学。
### 1. 高阶组件（Higher Order Components, HOCs）
在React中进行逻辑重用的强大模式。
```jsx
function withLogging(WrappedComponent) {
  return class extends React.Component {
    componentDidMount() {
      console.log(`Logged component: ${WrappedComponent.name}`);
    }
    render() {
      return <WrappedComponent {...this.props} />;
    }
  };
}
```

### 2. 渲染属性（Render Props）
共享代码逻辑时的可选模式。
```jsx
class MouseTracker extends React.Component {
  state = { x: 0, y: 0 };

  handleMouseMove = (event) => {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  };

  render() {
    return (
      <div onMouseMove={this.handleMouseMove}>
        {this.props.render(this.state)}
      </div>
    );
  }
}
```

### 3. 上下文API（Context API）
跨组件共享数据，减少组件层级传递。
```jsx
const ThemeContext = React.createContext('light');

class ThemedButton extends React.Component {
  render() {
    return (
      <ThemeContext.Consumer>
        {theme => <Button theme={theme} />}
      </ThemeContext.Consumer>
    );
  }
}
```

### 4. 异步组件加载
使用`React.lazy`提升加载效率。
```jsx
const LazyComponent = React.lazy(() => import('./SomeComponent'));

function MyComponent() {
  return (
    <React.Suspense fallback={<div>Loading...</div>}>
      <LazyComponent />
    </React.Suspense>
  );
}
```

### 5. 使用钩子函数（Hooks）
以函数组件形式使用状态和React特性。
```jsx
function Example() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

### 6. 自定义钩子（Custom Hooks）
提取组件逻辑，实现复用。
```jsx
function useWindowSize() {
  const [size, setSize] = useState([window.innerHeight, window.innerWidth]);
  useEffect(() => {
    const handleResize = () => {
      setSize([window.innerHeight, window.innerWidth]);
    };
    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  }, []);
  return size;
}
```

### 7. 访问前一个状态值
在hooks中获取前一个状态或属性值。
```jsx
function Timer() {
  const [count, setCount] = useState(0);
  const prevCountRef = useRef();
  useEffect(() => {
    prevCountRef.current = count;
  });
  const prevCount = prevCountRef.current;
  // 使用 prevCount 可参照以前的count值
}
```

### 8. 代码拆分
通过动态`import()`语句进行代码拆分，优化应用加载时间。
```jsx
import('./Math').then(math => {
  console.log(math.add(16, 26));
});
```

### 9. 使用Fragments
无需额外元素包裹组件子列表。
```jsx
class Table extends React.Component {
  render() {
    return (
      <table>
        <tbody>
          <tr>
            <Columns />
          </tr>
        </tbody>
      </table>
    );
  }
}

class Columns extends React.Component {
  render() {
    return (
      <React.Fragment>
        <td>Hello</td>
        <td>World</td>
      </React.Fragment>
    );
  }
}
```

### 10. 错误边界（Error Boundaries）
捕捉子组件树中JavaScript错误并记录这些信息。
```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }
  static getDerivedStateFromError() {
    return { hasError: true };
  }
  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong.</h1>;
    }
    return this.props.children;
  }
}
```

### 11. Portals
将子节点渲染到存在于父组件之外的DOM节点。
```jsx
class Modal extends React.Component {
  render() {
    return ReactDOM.createPortal(
      this.props.children,
      document.getElementById('modal-root')
    );
  }
}
```

### 12. 使用Refs转发
转发refs到组件树更深层次的位置。
```jsx
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} {...props}>
    {props.children}
  </button>
));
```

### 13. 性能优化——shouldComponentUpdate
减少不必要的渲染，优化性能。
```jsx
class List extends React.Component {
  shouldComponentUpdate(nextProps) {
    return nextProps.list !== this.props.list;
  }
}
```

### 14. 性能优化——使用React.memo
对函数组件应用记忆技术。
```jsx
const MyComponent = React.memo(function MyComponent(props) {
  /* render using props */
});
```

### 15. 使用PropTypes验证属性
确保组件收到正确类型的属性。
```jsx
MyComponent.propTypes = {
  // 你可以声明一个 prop 是特定的 JS 原始类型。默认
  // 情况下，这些 prop 都是可传可不传的。
  optionalNumber: PropTypes.number,
  
  // 你也可以用 `isRequired` 随着任何其他的校验方法来确保在不传入该 prop 的时候显示一个警告。
  requiredFunc: PropTypes.func.isRequired,
}
```

### 16. 使用Context替代Redux
在符合的场景下利用Context实现状态管理，避免过度使用Redux。
```jsx
const ThemeContext = React.createContext('light');

class ThemeButton extends React.Component {
  // 使用 contextType 读取当前的 theme context。
  // React 会通过 ThemeButton 组件以上最近的匹配的 ThemeProvider 找到当前的 theme 值。
  static contextType = ThemeContext;
}
```

### 17. 使用Suspense处理数据获取
对异步数据依赖进行优雅的处理。
```jsx
const resource = fetchData();

function Component() {
  // 使用 resource.read() 读取异步数据，如果数据还没有被获取完成，Suspense 会显示 fallback 中定义的内容
  const data = resource.read();
  return <div>{data}</div>;
}
```

### 18. 使用useReducer管理复杂状态逻辑
在hooks中进行状态管理，替代Redux。
```jsx
function todosReducer(state, action) {
  switch (action.type) {
    case 'add':
      return [...state, {
        text: action.text,
        completed: false
      }];
    // 其他 action handlers
    default:
      return state;
  }
}

function Todos() {
  const [todos, dispatch] = useReducer(todosReducer, []);

  return (
    <div>
      {todos.map((todo, index) => (
        <Todo key={index} todo={todo} dispatch={dispatch} />
      ))}
    </div>
  );
}
```

### 19. 利用动态import实现组件懒加载
结合React.lazy和Suspense，按需加载组件。
```jsx
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <React.Suspense fallback={<div>Loading...</div>}>
        <OtherComponent />
      </React.Suspense>
    </div>
  );
}
```

### 20. 使用useCallback减少子组件重复渲染
在渲染性能关键的应用中，通过记忆回调函数来减少子组件的不必要渲染。
```jsx
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```

### 21. 使用useRef访问DOM节点
直接操作DOM节点，实现跨渲染周期的状态共享。
```jsx
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    inputEl.current.focus();
  };
  
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```

### 22. 使用useLayoutEffect同步执行副作用
确保在浏览器绘制前，同步更新DOM状态。
```jsx
function useLockedBody() {
  useLayoutEffect(() => {
    const originalOverflow = window.getComputedStyle(document.body).overflow;
    document.body.style.overflow = 'hidden';
    return () => (document.body.style.overflow = originalOverflow);
  }, []); // Empty array ensures effect is only run on mount and unmount
}
```

### 23. 状态提升
当多个组件需要共享状态时，将状态提升至它们的公共父组件。
```jsx
class Parent extends React.Component {
  state = { color: 'red' };

  handleColorChange = (color) => {
    this.setState({ color });
  };

  render() {
    return (
      <div>
        <Child1 color={this.state.color} onColorChange={this.handleColorChange} />
        <Child2 color={this.state.color} />
      </div>
    );
  }
}
```

### 24. 使用useImperativeHandle自定义暴露给父组件的实例值
在使用forwardRef时，自定义父组件可以使用的ref操作。
```jsx
function FancyInput(props, ref) {
  const inputRef = useRef();
  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    }
  }));
  return <input ref={inputRef} />;
}
FancyInput = forwardRef(FancyInput);
```

### 25. 使用useEffect替代生命周期方法
以函数组件的形式复现生命周期方法的行为。
```jsx
function Example() {
  useEffect(() => { // 类似 componentDidMount 和 componentDidUpdate:
    // 更新文档的标题
    document.title = `You clicked ${count} times`;

    return () => { // 类似 componentWillUnmount
      // 卸载相关操作
    };
  }, [count]); // 仅在 count 更改时更新
}
```