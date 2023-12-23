## 大型React前端工程基础配置实践

React是当前最流行的前端开发框架之一，它具有高效、组件化、可扩展等特点，非常适用于大型前端工程的开发。在实际的工程开发中，为了提高开发效率、优化用户体验，我们通常会配置一些基础设置。本文将介绍如何进行大型React前端工程的基础配置实践。

### 一、引入依赖
入口文件作为整个项目的基础配置，从依赖上就可以看出其基础配置有哪几个模块组成；对于一般大型项目其依赖大概可以分成以下几类：
- 1. react
- 2. redux
- 3. antd
- 4. network - api
- 5. international
- 6. router
- 7. css

```javascript
// react
import React, { useState, useEffect } from "react";

// store相关
import { Provider } from "react-redux";
import { storeInstance } from "./store";

// 组件库
import { ConfigProvider, theme } from "antd";

// 网络请求--网络数据
import {
  api1,
  getLocaleFromLocalStorageFromLocalStorage,
  api2,
  api3,
} from "./utils/api";

// 国际化相关
import { IntlProvider } from "react-intl";
import enUS from "antd/locale/en_US";
import zhCN from "antd/locale/zh_CN";
import { initKeys, initLocales } from "./locales";

// 路由以及核心组件
import { BrowserRouter, useNavigate } from "react-router-dom";
import App from "./routes";

// 全局样式文件
import "./index.less";
```

### 二、动态修改网站icon的函数
下面的这个函数提供了修改网站icon的能力，其原理就是找到head标签，然后在其中新增一个link标签，通过此link标签就可以设置、修改网站过的icon了！

```javascript
function setBroIcon(): void {
  const constant = api1();
  const imgSrc = constant?.features?.browserIcon;
  if (imgSrc) {
    const link = document.createElement("link");
    link.rel = "icon";
    link.href = `${imgSrc}`;
    document.getElementsByTagName("head")[0].appendChild(link);
  }
}
```

### 三、国际化支持

对于大型前端工程来说，国际化是一个重要的考虑因素。因此需要引入了React Intl库来实现多语言支持：

```javascript
const [lang, setLang] = useState(getLocaleFromLocalStorageFromLocalStorage()); // 获取localStorage中的语言类型
const [message, setMessage] = useState(initKeys); // 设置国际化资源
// const navigate = useNavigate();
// const [reload, setReload] = useState(false);
```

使用useState钩子来管理语言和国际化信息的状态。通过调用相应的API，可以获取当前的语言设置和对应的文案资源。在语言切换或文案资源更新时，使用setMessage和setLang函数来更新状态值。

### 四、事件处理

接下来，需要对页面上与用户交互相关的事件进行处理，以便进行相应的操作。例如：

```javascript
document.oncontextmenu = function (event) {
  event.preventDefault();
};
```
上述代码屏蔽了浏览器的默认右键菜单，以防止用户在页面上弹出默认的菜单选项。

```javascript
document.addEventListener('mousewheel', function (e) {
  if (((e as any)?.wheelDelta && (event as any)?.ctrlKey) || (e as any).detail) {
    event.preventDefault();
  }
}, {
  capture: false,
  passive: false
});
document.addEventListener('keydown', function (event) {
  if ((event.ctrlKey === true || event.metaKey === true) && (event.keyCode === 61 || event.keyCode === 107 || event.keyCode === 173 || event.keyCode === 109 || event.keyCode === 187 || event.keyCode === 189)) {
    event.preventDefault();
  }
}, false);
```

上述代码屏蔽了浏览器的缩放功能和特定的键盘操作，以防止用户对页面进行缩放或使用快捷键进行特殊操作。

### 五、定时清空控制台信息

```javascript
useEffect(() => {
  const id = setInterval(() => {
    console.clear();
  }, 10 * 60 * 1000);
  return () => {
    clearInterval(id);
  };
}, []);
```

以上代码利用了React的useEffect钩子，在组件加载后每隔10分钟清除一次控制台信息。这样可以避免因为控制台输出过多导致页面卡顿的问题。

### 六、全局信息配置

在入口文件中进行组件的渲染。使用Context，将状态管理和多语言支持的相关信息传递给子组件：

```javascript
return reload ? (
  <></>
) : (
  <ConfigProvider
    theme={{
      algorithm: theme.compactAlgorithm,
    }}
    locale={lang !== "zh-CN" ? enUS : zhCN}
  >
    <Provider store={storeInstance}>
      <IntlProvider locale={lang} messages={message}>
        <App />
      </IntlProvider>
    </Provider>
  </ConfigProvider>
);
```

- 使用ConfigProvider提供了一些全局的配置项，比如主题和布局
- 使用Provider将状态管理的上下文信息传递给子组件
- 使用IntlProvider实现了多语言支持

### 七、内部更新
在在组件中监听特定几个事件，事件被触发的时候就强行刷新根组件，从而导致整个页面刷新；从而实现**内部强制刷新整个页面的功能**

```jsx
  useEffect(() => {
    const OtherReload = () => {
      setReload(true);
      setTimeout(() => {
        setReload(false);
      }, 100);
    };
    const handleLangChange = async (event) => {
      await initLocales();
      setMessage(() => initKeys);
      setLang(() => getLocaleFromLocalStorage());
      OtherReload();
    };

    const refresh = () => {
      OtherReload();
    };

    document.addEventListener("language-change", handleLangChange);
    document.addEventListener("other-reload", publishReload);
    document.addEventListener("force-refresh", refresh);
    return () => {
      document.removeEventListener("publish-reload", OtherReload);
      document.removeEventListener("other-change", handleLangChange);
      document.removeEventListener("force-refresh", refresh);
    };
  }, [navigate, setReload, setMessage, setLang]);
  ```

### 八、路由管理

将整个应用包裹在BrowserRouter组件中，实现路由管理的功能：

```javascript
export default function Root(props) {
  return (
    <BrowserRouter>
      <BigWrapper />
    </BrowserRouter>
  );
}
```

## 全部代码
```jsx
// react
import React, { useState, useEffect } from "react";

// store相关
import { Provider } from "react-redux";
import { storeInstance } from "./store";

// 组件库
import { ConfigProvider, theme } from "antd";

// 网络请求--网络数据
import {
  api1,
  getLocaleFromLocalStorageFromLocalStorage,
  api2,
  api3,
} from "./utils/api";

// 国际化相关
import { IntlProvider } from "react-intl";
import enUS from "antd/locale/en_US";
import zhCN from "antd/locale/zh_CN";
import { initKeys, initLocales } from "./locales";

// 路由以及核心组件
import { BrowserRouter, useNavigate } from "react-router-dom";
import App from "./routes";

// 全局样式文件
import "./index.less";

function setBroIcon(): void {
  const constant = api1();
  const imgSrc = constant?.features?.browserIcon;
  if (imgSrc) {
    const link = document.createElement("link");
    link.rel = "icon";
    link.href = `${imgSrc}`;
    document.getElementsByTagName("head")[0].appendChild(link);
  }
}

function BigWrapper(props) {
  const [lang, setLang] = useState(getLocaleFromLocalStorage());
  const [message, setMessage] = useState(initKeys);
  const navigate = useNavigate();
  const [reload, setReload] = useState(false);

    setBroIcon();

  // 屏蔽浏览器默认右键菜单
  document.oncontextmenu = function (event) {
    event.preventDefault();
  };

  // 屏蔽浏览器缩放功能
  document.addEventListener('mousewheel', function (e) {
      if (((e as any)?.wheelDelta && (event as any)?.ctrlKey) || (e as any).detail) {
        event.preventDefault();
      }
    }, {
      capture: false,
      passive: false
  });
  document.addEventListener('keydown', function (event) {
      if ((event.ctrlKey === true || event.metaKey === true) && (event.keyCode === 61 || event.keyCode === 107 || event.keyCode === 173 || event.keyCode === 109 || event.keyCode === 187 || event.keyCode === 189)) {
        event.preventDefault();
      }
  }, false);

  useEffect(() => {
    const id = setInterval(() => {
      console.clear();
    }, 10 * 60 * 1000);
    return () => {
      clearInterval(id);
    };
  }, []);

  useEffect(() => {
    const OtherReload = () => {
      setReload(true);
      setTimeout(() => {
        setReload(false);
      }, 100);
    };
    const handleLangChange = async (event) => {
      await initLocales();
      setMessage(() => initKeys);
      setLang(() => getLocaleFromLocalStorage());
      OtherReload();
    };

    const refresh = () => {
      OtherReload();
    };

    document.addEventListener("language-change", handleLangChange);
    document.addEventListener("other-reload", publishReload);
    document.addEventListener("force-refresh", refresh);
    return () => {
      document.removeEventListener("publish-reload", OtherReload);
      document.removeEventListener("other-change", handleLangChange);
      document.removeEventListener("force-refresh", refresh);
    };
  }, [navigate, setReload, setMessage, setLang]);

  return reload ? (
    <></>
  ) : (
    <ConfigProvider
      theme={{
        algorithm: theme.compactAlgorithm,
      }}
      locale={lang !== "zh-CN" ? enUS : zhCN}
    >
      <Provider store={storeInstance}>
        <IntlProvider locale={lang} messages={message}>
          <App />
        </IntlProvider>
      </Provider>
    </ConfigProvider>
  );
}

export default function Root(props) {
  return (
    <BrowserRouter>
      <BigWrapper />
    </BrowserRouter>
  );
}

```