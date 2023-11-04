本文提供两种在前端项目中配置TailwindCss的方法，希望能够帮助到被配置烦扰的同学。
## 1. 在CRA项目中配置tailwindcss
1. **eject前准备**
```
git init && git add . && git commit -m "init"
```
2. **释放配置**
```
yarn eject
```
3. **初始化tailwindcss的配置**
```
npx tailwindcss init
```
4. **修改其中的配置**
```
content: ["./src/**/*.{ts,tsx,js,jsx}", "./public/index.html"],
```
5. **引入tailwindcss的预置样式**
在`src/index.css`文件头部增加：
```
@tailwind base;
@taiwind components;
@tailwind utilities;
```
6. **使用index.css**
在`src/index.tsx`中引入index.css：
```
import "./index.css";
```
7. **测试**
使用下面的代码测试是否配置成功：
```
<div className="bg-blue-300 text-white p-4">Hello, Tailwind Css !</div>
```
8. **运行项目**
```
yarn start
```
## 2. 在非CRA项目中配置tailwindcss
1. **使用webpack-cli快速生成基础配置**
```
yarn init -y
npx webpack init
```
选择"y"，然后再次运行该命令。
2. **卸载自动安装的postcss和loader**
```
yarn remove postcss postcss-loader
```
3. **安装tailwindcss及配套的postcss**
```
yarn add postcss@8.4.4 postcss-flexbugs-fixes@5.0.2 postcss-loader@6.2.1 postcss-normalize@10.0.1 postcss-preset-env@7.0.1 tailwindcss@3.0.2
```
4. **初始化tailwindcss配置项**
```
npx tailwindcss init
```
5. **修改其中的配置**
```
content: ["./src/**/*.{ts,tsx,js,jsx}", "./index.html"],
```
6. **创建样式文件**
```
touch src/index.css
```
7. **在样式文件中引入tailwindcss**
```
@tailwind base;
@taiwind components;
@tailwind utilities;
```
8. **在src/index.ts中使用index.css**
```
import "./index.css";
```
9. **在index.html中使用样式**
```
<div class="bg-blue-300 text-white p-4">Hello, Tailwind Css !</div>
```
10. **修改webpack.config.js中的配置**
替换`postcss-loader`的配置为：
```javascript
{
  loader: require.resolve('postcss-loader'),
  options:{
    postcssOptions:{
      ident: "postcss",
      config: false,
      plugins: !useTailWind ? [
        "postcss-flexbugs-fixes",
        ["postcss-preset-env", {
          autoprefixer: {flexbox: "no-2019"},
          stage: 3,
        }],
        "postcss-normalize",
      ]:[
        "tailwindcss",
        "postcss-flexbugs-fixes",
        ["postcss-preset-env", {
          autoprefixer: {flexbox: "no-2019"},
          stage: 3,
        }],
      ]
    }
  }
}
```
其中，`const useTailWind = fs.existsSync(path.join("./", "tailwind.config.js"));` 用于检查是否存在`tailwind.config.js`文件，以确定是否使用tailwindcss。
11. **测试**
```
yarn serve
yarn build:prod
```
以上是在CRA项目和非CRA项目中配置tailwindcss的步骤和代码示例。