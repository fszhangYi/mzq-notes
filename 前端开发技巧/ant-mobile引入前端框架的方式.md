# ant-mobile引入前端框架的方式

在前端开发中，选择合适的前端框架对于快速构建现代化的网页应用至关重要。本文将以ant-mobile作为示例，介绍如何引入和配置该前端框架。

### 1. 准备工作
在项目中安装ant-mobile的依赖包。可以使用npm或者yarn命令来进行安装，如下所示：

```
npm install antd-mobile antd-mobile-v2
```

或者

```
yarn add antd-mobile antd-mobile-v2
```

### 2. 引入样式和组件

在项目的入口文件中（通常是App.js或index.js），需要引入ant-mobile的样式和组件，以便在整个应用中使用它们。

首先，引入ant-mobile的样式文件，它们通常被放置在项目的css文件夹中，可以使用相对路径或别名路径导入。示例代码如下：

```jsx
import "antd-mobile/bundle/css-vars-patch.css";
import "antd-mobile-v2/dist/antd-mobile.css";
```

接下来，引入需要使用的ant-mobile组件和本地化配置。总的引入代码如下：

```jsx
import React from "react";
import enUS from "antd-mobile-v2/lib/locale-provider/en_US";
import { LocaleProvider } from "antd-mobile-v2";
import { ConfigProvider } from "antd-mobile";
import Router from "./router/index.js";
import "./App.less";
```

### 3. 配置本地化

ant-mobile支持本地化功能，可以根据不同的语言环境显示对应的文本。在上述代码中，引入了enUS语言包，并使用LocaleProvider组件来配置默认的语言环境。代码如下：

```jsx
const renderLocale = component => {
  return (
    <LocaleProvider locale={enUS}>
      <ConfigProvider>
        {component}
      </ConfigProvider>
    </LocaleProvider>
  );
};
```

### 4. 包裹并渲染路由组件

最后，在App组件的render方法中，使用renderLocale函数将Router组件包裹起来，并返回渲染结果。示例代码如下：

```jsx
class App extends React.Component {
  componentDidMount(){}

  render() {
    return <div className="global-container">{renderLocale(<Router />)}</div>;
  }
}

export default App;
```

### 5. 挂载渲染器
将renderLocale函数挂载在App对象上，作为其静态方法：
```jsx
App.renderLocale = renderLocale;
```

### 6. 亮点
这种架构的亮点有两个，一个是renderLocale函数，这个函数中断了App的正向渲染，

一个是将renderLocale挂在App对象上，这样的话就获得一定的自主权，如下所示：
```jsx
ReactDOM.render(
  App.renderLocale(
    newRouter
  ),
  newWrapper
);
```
也就是实现了**组件库配置不变，路由改变的功能**！(App不仅可以作为渲染的组件，也可以作为js中的普通对象，进而调用其上的方法！这一点还是比较新颖的)

## 结语

通过以上步骤，我们成功地引入了ant-mobile前端框架，并进行了必要的配置。如此，可以在项目中使用ant-mobile提供的丰富组件和本地化功能来构建功能强大的移动端应用程序。

## 所有代码
```jsx
import React from "react";
import enUS from "antd-mobile-v2/lib/locale-provider/en_US";
import { LocaleProvider } from "antd-mobile-v2";
import { ConfigProvider } from "antd-mobile";
import "antd-mobile/bundle/css-vars-patch.css"
import "antd-mobile-v2/dist/antd-mobile.css";
import Router from "./router/index.js";
import "./App.less";

const renderLocale = component => {
  return (
    <LocaleProvider locale={enUS}>
      <ConfigProvider>
        {component}
      </ConfigProvider>
    </LocaleProvider>
  );
};

class App extends React.Component {
  componentDidMount(){}

  render() {
    return <div className="global-container">{renderLocale(<Router />)}</div>;
  }
}

App.renderLocale = renderLocale;

export default App;
```