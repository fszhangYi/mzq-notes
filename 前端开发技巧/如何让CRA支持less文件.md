使用`npx create-react-app demo --template=typescript`创建出来的CRA前端项目天然是不支持less文件的，所以想要创建出来的前端项目支持less文件就必须手动配置。本文叙述了在CRA前端项目中配置less的步骤：

## 1. 在项目根目录，通过运行以下命令，安装 `react-scripts` 的最新版本：
```bash
yarn add @craco/craco craco-less --dev
```
## 2. 在项目根目录，创建 `config-overrides.js` 文件。
```bash
touch config-overrides.js
```

## 3. 在 `config-overrides.js` 中，添加以下内容：
```javascript
const CracoLessPlugin = require('craco-less');

module.exports = {
  plugins: [
    {
      plugin: CracoLessPlugin,
      options: {
        lessLoaderOptions: {
          lessOptions: {
            modifyVars: { '@primary-color': '#1DA57A' }, // 可以在此处设置LESS变量
            javascriptEnabled: true,
          },
        },
      },
    },
  ],
};

```
## 4. 在 `package.json` 中，将 `scripts` 部分中的 `react-scripts` 替换为 `cra-scripts`，如下所示：
```json
"scripts": {
  "start": "craco start",
  "build": "craco build",
  "test": "craco test",
  "eject": "craco eject"
},

```
## 5. 安装 `less` 和 `less-loader`，通过运行以下命令：
```bash
yarn add less less-loader --save-dev

```
## 6. 确保你的 Less 样式文件以 `.less` 扩展名结尾，并在你的组件中引入它们。
配置完成后，CRA 将能够解析和应用 less 样式。你可以在你的项目中使用 less 文件来定义样式，并通过导入它们到组件中使用。