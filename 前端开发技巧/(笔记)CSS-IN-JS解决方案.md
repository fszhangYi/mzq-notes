所谓css-in-js指的就是：将css代码集成在js中。

# 本笔记包括三个专题的内容：
- 1. 为什么会有css-in-js
- 2. css-in-js介绍
- 3. Emotion库

## 为什么：css-in-js的解决方案提出的原因：
- 1. 能够突破css的一些局限性
    - 缺乏动态功能
    - 没有作用域的概念：指的是`import 'index.css';`这种做法会导致样式在全局中生效；而css-in-js的解决方案中就可以使用js的作用域充当css的作用域
    - 不具备可移植性
- 2. css-in-js的解决方案本质上是实现了all-in-js

### CSS-IN-JS 方案的优点总结：
1.  让 CSS 代码拥有独立的作用域，防止 CSS 代码的覆盖和冲突，防止样式冲突。
1.  让组件更具可移植性，实现开箱即用，轻松创建独立部署的应用程序。
1.  让组件更具可重用性，只需编写一次即可，可以在任何地方运行。不仅可以在同一应用程序中重用组件，而且可以在其他应用程序或者全局范围重用组件。
1.  让样式具有动态功能，可以将复杂的逻辑混入用于样式规则，如根据创建逻辑的方式动态的生成规则，它是理想的解决方案。

### CSS-IN-JS 方案的缺点总结：
1.  为项目增加了额外的复杂性。
1.  会自动生成选择器，这一点大大降低了代码的可读性。

## 1. Emotion库初步使用
### 介绍
Emotion库本质上是一个使用js编写css样式的库。
### 安装
`npm install @emotion/core @emotion/styled`
### 原理
Emotion库生效是通过名为```css```的属性实现的。其实现方案有两种：
#### 1. JSX Pragma
使用这种方法，可以通知babel无需将jsx语法转换成React.createElement方法而是转成jsx方法。
```jsx
/** @jsx jsx*/
import {jsx} from '@emotion/core';
```
上述代码块中的注释是必不可少的！

加上上述代码之后，下面的组件中的css属性就可以被解析器正确的解析了！
```jsx
import React from 'react';
/** @jsx jsx */
import { jsx } from '@emotion/core';

function App() {
  return <div css={{ width: 200, height: 200, background: 'red' }}>App works</div>;
}

export default App;
```

#### 2. Babel preset
需要自行配置babel，如果使用的是CRA框架，则需要首先执行`npm run eject && npm install @emotion/babel-preset-css-prop`,然后在package.json的babel属性下面增加内容：
```json
"presets": [
    "react-app",
    "@emotion/babel-preset-css-prop",
]
```

然后，下面的代码可以直接执行：
```jsx
import React from 'react';

function App() {
  return <div css={{ width: 200, height: 200, background: 'red' }}>App works</div>;
}

export default App;
```

## 2. Emotion库中的css属性和方法
### css方法 -- 有模板字符串和普通函数两种使用方式
css方法需要和css属性搭配使用，如下所示：
```js
const style = css`
    width: 100px;
    height: 100px;
    background: skyblue;
`;
// style的本质是一个对象 即 instanceof Object === true

<div css={style}> App works ... </div>
```

第二种使用css方法的方式：
```js
cosnt style = css({
    width: 200,
    height: 200,
    background: 'red',
});

function App() {
    return <div css={style}>App works . </div>;
}
```

推荐使用第一种方式，因为模板字符串的内容和css文件中相同，有利于平稳回退。

### css属性优先级
从组件的props对象中得到的css属性的优先级高于组件内部css属性的优先级。因此在调用组件的时候可以覆盖组件的默认样式。通俗点来说就是引用此组件的父组件为其设置的css属性比此组件内部的css属性的优先级更高！

下面是验证代码：

```jsx
// 父组件
import Css from './Css';
import { css } from '@emotion/core';

const style = css`
  background: pink;
`;

function App() {
  return <div>
    <Css css={style}/>
  </div>;
}

export default App;
```

```jsx
// 子组件
import React from 'react';
import { css } from '@emotion/core';

const style = css`
  width: 200px;
  height: 200px;
  background: skyblue;
`;

function Css(props) {
  return <div css={style} {...props}>Css</div>;
}

export default Css;
```
## 3. emotion库的使用
### 3.1 样式化组件 -- 同样也是两种方式
样式化组件是专门用来绘制界面的组件，它有别于一般模板组件。样式化组件时emotion库提供的另外一种为元素添加样式的方式。

引入：
`import styled from '@emotion/styled';`

使用：
```jsx
const Button = styled.button`
    width: 100px;
    color: red;
`;
```

或者可以写成：
```jsx
const Button = styled.button({
    width: 100,
    color: 'green',
});
```

### 3.2 覆盖样式化组件中的样式的三种方式 --在styled方法中通过外部的props对象覆盖之前的样式
同样，根据styled方法的调用方式，也有两种使用props外传属性的方法：

```jsx
const Button = styled.button`
    width: 100px;
    height: 30px;
    background: ${props=>props.bgColor??'skyblue'};
`;

<Button bgColor={'red'} />
```

或者，

```jsx
cost Container = styled.div(props=>{
    width: props.width ?? 1000,
    background: 'pink',
    margin: '0 auto',
});

<Container width={300} />
```

又或者，

```jsx
const Div = styled.button({
    color: 'red',
}, props=>({
    color: props.color,
}));
```

个人觉得最后一种做法非常的优美！值得推荐。

### 3.3 使用styled方法为任何组件添加样式--也包括自定义的样式组件
如下图所示：
```jsx
const Demo = ({className}) => <divclassName = {className}>Demo</div>;

const Fancy = styled(Demo)`
    color: red;
`;
```

当然，也有别的做法：

```jsx
onst Demo = ({className}) => <divclassName = {className}>Demo</div>;

const Fancy = styled(Demo)({
    color: 'green',
});
```

### 3.4 使用styled为特定层级的子组件添加样式覆盖
styled方法的另外一个强大之处在于可以对外层组件的所有子组件设置自定义样式，如下面的demo所示：

```jsx
const Child = styled.div`
    color: red;
`;

const Parent = styled.div`
    ${Child}{
        color: green;
    }
`;
```

上述的代码表示的含义就是：如果Child组件单独使用的话那么它的文字的颜色就是红色，如果被Parent组件所包裹，那么文字颜色就会变成绿色。

同样，也有两种做法：
```jsx
const Child = styled.div({
    color: 'red';
});

const Parent = styled.div(
    [Child]: {
        color: 'green';
    }
);
```

验证用代码：
```jsx
function App() {
    return (
        <div>
            <Child>Child</Child>
            <Parent><Child>parent</Child></Parent>
        </div>
    )
}
```

### 3.5 emotion中的&样式选择器
&主要的作用是为了做嵌套选择的，&的本意是组件本身，比如`& > a`表示的就是选中此组件的a子标签。
```jsx
const Container = styled.div`
    color: red;
    & > a {
        color: pink;
    }
`;
```

使用示例：
```jsx
import styled from '@emotion/styled'

const Container = styled.div`
  height: 200px;
  background: skyblue;
  color: pink;
  &:hover {
    background: pink;
  }
  & > span {
    color: yellow;
  }
`;

function App() {
  return (
    <div>
      <Container>
        container
        <span>span</span>
      </Container>
    </div>
  );
}

export default App;
```

### 3.6 emotion库作用下as属性的作用
有时候即希望使用某个组件的样式，但又不想呈现原来的标签，这个时候，emotion库提供了名为**as**的属性，此属性的作用就是将一个组件变成另外一个。

适用场景：比如使用a标签表示一个button

```jsx
const Button = styled.button`
    color: red;
`;

<Button as='a' href='#'> button </Button>
```

具体的路线就是：button -> Button -> a

经过styled方法处理，所有想要用到的样式就被过滤出来了！非常神奇！

### 3.7 为同一个组件添加多个样式的做法
需要注意的是，使用数组的方式为一个组件添加多个样式的做法中，如果出现样式之间的重名，那么后面设置的样式将会覆盖前面的样式。

```jsx
import { css } from '@emotion/core'

const base = css`
  color: yellow;
`;

const dangerous = css`
  color: red;
`;

<button css={[base, dangerous]}>button</button>
```

### 3.8 global组件 -- 用来定义全局样式
```jsx
import {css, Global} from '@emotion/core';

const styles = css`
    body {margin:0;}
`;

function App () {
    return <>
        <Global styles={styles} />
        App works ...
    </>
}
```

需要注意的点就在于，这里使用的是styles而不是style，不要拼写错了。此外还有就是在入口组件中以组件的形式引入全局样式，如上面所示。

### 3.9 emotion库对css动画的支持
正如同styled提供了修改样式的方法，emotion中提供的keyframes方法可以用来做css动画。 

keyframes方法的返回值是一个帧动画，此帧动画要在css中的animation中使用。

```jsx
import {css, keyframes} from '@emotion/core';
const move = keyframes`
  0% { left: 0; top: 0; background: pink; }
  100% { top: 300px; left: 600px; background: skyblue; }
`;

const box = css`
  width: 100px;
  height: 100px;
  position: absolute;
  animation: ${move} 2s ease infinite alternate;
`;

function App() {
  return <div css={box}>App works ...</div>;
}

```

### 3.10 emotion提供的主题库使用方法
先下载主题以来：`npm install emotion-theming`

然后引入：
```
import {ThemeProvider } from 'emotion-theming';
```

其作用是为了存储主题样式，方便其它组件获取主题样式。

使用的时候将此Provider放在可视组件的最外层。
```jsx
function App () {
    return <ThemrProvider></ThemeProvider>
}

```

接下来就是对主题的定义和使用了：
```jsx
const theme = {
    colors: {
        primary: 'hotpink',
    }
}

<ThemeProvider theme={theme}><App /></ThemeProvider>
```

设置好的主题会被自动注入到以css方法调用结果作为返回值的函数的形参中去，因此在其他子组件中可以通过构造一个返回css方法调用结果的函数来使用设置好的主题。
```jsx
const getPrimaryColor = props => css`
    color: ${props.colors.primary}
`;

<div css={getPrimaryColor}></div>
```

除了在css方法中可以获得/使用主题样式，还可以通过钩子函数使用设置的主题样式：
```jsx
import {useTheme} from 'emotion-theming';

function Demo () {
    const theme = useTheme(); // 这个theme就是在ThemeProvider中注入的对象
}
```