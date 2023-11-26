在前端开发过程中，有时候需要用到本地配置，比如一些api的key或者对于每个开发者私有的配置(开发者本地ip等)。这些配置信息不能直接放到git仓库中去，一方面是会暴露隐私信息，另一方面会干扰到其他开发的使用。

本文叙述了如何将本地配置文件注入到前端项目中去的方法和步骤：

首先需要使用dotenv第三方库使本地配置在node环境下可用，[关于dotenv的使用可见我的另一篇文章]([使用dotenv库的基本步骤 - 掘金 (juejin.cn)](https://juejin.cn/post/7304558952179302436))

然后就是如何让这些配置在浏览器中也生效，这里分两种情况讨论：

## 1. 不使用框架
如果前端项目是开发自己使用webpack搭建的，那么只需要在webpack中进行一些额外的配置就可以直接在浏览器环境中使用process这个对象了，具体来说就是使用名为webpack.DefinePlugin的插件，额外的配置代码如下：
```js
const webpack = require('webpack');

module.exports = {
  // ... 其他配置
  plugins: [
    // 定义环境变量
    new webpack.DefinePlugin({
      'process.env': JSON.stringify(process.env),
    }),
  ],
  // ... 其他配置
};

```

## 2. 使用前端框架
这里以RCA为例。RCA已经做了相关的处理，只需要保证将要注入到浏览器中的本地配置信息字段名以**REACT_APP**开头即可！
```env
REACT_APP_OPENAI_KEY=sk-7HFYCtmUItc9Y2MG8642E896C61a42Aa87D3Cc7a***********
```

在打包的时候CRA框架就会自动将代码中的process.env.REACT_APP_OPENAI_KEY替换成对应的值！也就是说在代码中可以直接使用`process.env.REACT_APP_OPENAI_KEY`取到对应的值。

！！注意这并不意味`window.process.env.REACT_APP_OPENAI_KEY`是存在的，这是打包的时候被替换了而已！！