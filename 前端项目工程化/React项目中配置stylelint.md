# 项目中配置stylelint

## 什么是stylelint？

在开发项目中，经常需要处理样式表，如CSS、Sass、Less等。而随着项目规模的增大，样式表的数量也会越来越多，样式表的维护变得更加复杂。而stylelint作为一个样式表的静态分析工具，可以帮助发现和纠正样式表中的问题，提高项目的代码质量和规范。

## 配置依赖

首先，在项目中配置stylelint需要添加一些必要的依赖，因此需要使用yarn命令来安装这些依赖。执行以下命令：

```bash
yarn add -D \
sass \
sass-loader \
stylelint \
postcss \
postcss-scss \
postcss-less \
postcss-html \
stylelint-config-prettier \
stylelint-config-recess-order \
stylelint-config-recommended-scss \
stylelint-config-standard \
stylelint-config-standard-vue \
stylelint-scss \
stylelint-order \
stylelint-config-standard-scss \
stylelint-less
```

## 创建配置文件

在项目根目录下创建一个名为`.stylelintrc.cjs`的文件，并在其中编写具体的配置项。

```bash
touch .stylelintrc.cjs
```

然后在文件中添加以下内容：

```javascript
module.exports = {
    extends: [
        "stylelint-config-recommended",
        "stylelint-config-recess-order",
        "stylelint-config-recommended-scss",
        "stylelint-config-standard",
        "stylelint-config-standard-vue",
        "stylelint-config-standard-scss",
    ],
    rules: {
        // 在这里可以自定义的规则，覆盖默认的规则
    },
};
```

这样，就创建了一个基本的stylelint配置文件，并引入了一些常用的规则集。

## 配置忽略文件

通常希望stylelint忽略一些特定的文件，例如不希望它检查`node_modules`目录下的文件或者一些临时文件。为了实现这一点，需要在项目根目录下创建一个名为`.stylelintignore`的文件，并将需要忽略的文件或者目录路径写入该文件中。执行以下命令：

```bash
touch .stylelintignore
```

然后在文件中添加以下内容：

```
/node_modules/*
/dist/*
/html/*
/public/*
```

这样，stylelint在运行时将会忽略`node_modules`、`dist`、`html`和`public`目录下的文件。

## 添加运行脚本

为了方便在命令行中运行stylelint检查样式表，可以在`package.json`文件的`scripts`字段中添加一个运行脚本。打开`package.json`文件，然后在`scripts`字段中添加以下内容：

```json
"scripts": {
  "lint:style": "stylelint src/**/*.{css,scss,vue} --cache --fix"
}
```

这样，就可以使用`yarn lint:style`命令来运行stylelint检查样式文件了。它会检查`src`目录下所有后缀为`.css`、`.scss`和`.vue`的文件，并尝试自动修复一些错误。

## 自动校验格式

可以使用`lint-staged`来在项目提交代码时自动运行stylelint检查样式文件，并修复其中的一些错误。为此，可以在项目中安装`lint-staged`。执行以下命令：

```bash
yarn add -D lint-staged
```

然后，在`package.json`文件中添加以下内容：

```json
"lint-staged": {
    "src/**/*.{css,less}": [
      "prettier --write",
      "npx stylelint --fix",
      "git add"
    ]
}
```

这里的配置意思是在提交文件时，对`src`目录下所有后缀为`.css`和`.less`的样式文件进行自动格式化和修复，并将修改后的内容添加到git暂存区。

## 使用webpack集成stylelint

如果在项目中使用了webpack作为构建工具，可以将stylelint集成到webpack中，在开发或构建阶段对样式文件进行检查。

首先，需要安装一些webpack相关的插件。执行以下命令：

```bash
yarn add -D eslint-webpack-plugin@4.0.1 stylelint-webpack-plugin@4.1.1
```

然后，在webpack配置文件中引入这些插件并配置：

```javascript
const StylelintPlugin = require('stylelint-webpack-plugin');
const ESLintPlugin = require('eslint-webpack-plugin');

module.exports = {
    // ...其他配置项
    plugins: [
        // ...其他插件
        new ESLintPlugin({
            extensions: [
                'js', 
                'jsx',
                'ts', 
                'tsx',
            ],
        }),
        new StylelintPlugin({
            configFile: '.stylelintrc.cjs',
            files: '**/*.{css,scss}', 
        }),
    ],
    // ...其他配置项
};
```

使用上述配置后，当运行`webpack`时，它将会在构建过程中对样式文件进行检查。如果发现样式文件中存在问题，webpack将会在控制台输出错误信息，并阻止构建过程。

## 测试

为了测试的配置是否生效，可以新建一个样式文件，并添加一些混乱的样式代码进去。执行以下命令：

```bash
touch src/index.css
vim src/index.css
```

然后在`index.css`文件中添加一些混乱的样式代码。

接下来，运行`yarn lint:style`命令，这将会触发stylelint对`src`目录下的样式文件进行检查和修复。

## 小结

通过以上的步骤，实现了在项目中配置stylelint，能够帮助发现和纠正样式表中的问题。在开发过程中，运行stylelint检查样式代码，可以帮助保持样式表的一致性和规范性，提高代码质量和开发效率。同时，通过自动集成到构建工具中，可以在开发或构建阶段自动进行样式检查，避免样式问题导致的线上bug。