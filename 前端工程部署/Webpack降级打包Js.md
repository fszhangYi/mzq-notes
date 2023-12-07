当现代JavaScript在古老的浏览器环境中求生欲展现，Webpack联手Babel，便成为匹配双方的红娘。本文旨在介绍如何使用Webpack和Babel配合，对JavaScript代码进行降级处理，并明确指定代码版本，确保编译后的代码能在既定的运行环境中流畅执行。

## 准备工作

在开始之前，伟大的构建之旅需要进行一些准备：

1. 确保已经安装了Node.js和npm。  
2. 初始化npm项目（若尚未完成），在项目根目录运行 `npm init -y`。
3. 安装Webpack及其命令行工具 `npm install webpack webpack-cli --save-dev`。

## 安装Babel

现代JavaScript特性的向下兼容，需要依赖Babel。以下是安装所需依赖的步骤：

```bash
npm install @babel/core babel-loader @babel/preset-env --save-dev
```

## 配置Babel

Webpack通过配置文件理解项目构建规则，在项目的根目录创建`webpack.config.js`。此外，Babel的配置信息，常见为`.babelrc`或在Webpack配置文件中的`babel-loader`对象里；前者为专门的Babel配置文件，而后者将配置直接嵌入Webpack配置中。

创建`.babelrc`，并撰写如下配置：

```json
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": {
          "esmodules": true, // 或指定具体环境，如："chrome": "58", "ie": "11"
          "node": "current" // 若目标为Node.js，则使用当前的Node版本
        }
      }
    ]
  ]
}
```

该配置具体指明：Babel根据环境特性调整语法降级的级别。

## 配置Webpack

在`webpack.config.js`中配置Babel loader，给予Babel权力负责JavaScript文件的打包任务：

```javascript
const path = require('path');

module.exports = {
  entry: './src/index.js', // 项目入口文件
  output: {
    path: path.resolve(__dirname, 'dist'), // 打包文件的输出目录
    filename: 'bundle.js' // 打包后的文件名
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader' // 指定loader
        }
      }
    ]
  }
};
```

## 运行Webpack

一切准备就绪后，运行下方命令，启动Webpack构建流程：

```bash
npx webpack --config webpack.config.js
```

## 结语

降级并非退步，而是为更广泛的用户体验考虑。Webpack结合Babel，犹如时间旅行者，把创作者的智慧带给每一个年代，至此，网页应用的兼容性之桥搭建完毕，跨时代交流顺畅无阻。

## 附录
如不使用.babelrc作为配置文件（因为无法保证.babelrc和其他babel配置不冲突），可以将babel-loader在webpack中的使用和配置防在一处：
```js
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader' // 指定loader
          options: { 
              presets: [ 
                  ['@babel/preset-env', { targets: { browsers: '> 1%, not IE 11' } }], 
              ] 
          }
        }
      }
    ]
  }
```