---
title: React项目中使用module css
date: 2023-04-XX
tags: React, CSS Modules, Webpack, 前端开发
---

## 引言

在React的项目开发过程中，一直以来样式管理都是一个需要解决的问题。传统的全局CSS在大型应用中会导致命名冲突与维护困难，而使用CSS Modules的方式可以很好的解决这些问题。本文将详细介绍在React项目中如何使用module.css来实现样式的模块化与隔离，同时也将探讨相较于styled-components等其他CSS in JS解决方案，module.css带来的优势和配置方式。

## 什么是CSS Modules

CSS Modules是一种# React项目中使用module css

在当今的前端开发风潮中，组件化和模块化开发成为了主流。React项目也不例外，其致力于构建用户界面的组件化开发框架，每个组件都有自己的逻辑和样式。然而，随着组件数量增多和代码库扩大，必然会遇到样式冲突和覆盖等问题。为了避免这些问题的出现，样式模块化是一个有效的解决方案。而在React项目中使用module css或module.less就能实现样式的模块化和样式隔离。

## 为何选用module css而不是styled-components？

styled-components是React社区流行的样式方案之一，它采用JavaScript代码来编写样式。然而，在某些场景下，当通过styled-components包装第三方组件库中的组件时，若包装形式相同，React中生成的随机className也可能相同，这将导致样式冲突。因此，为了确保样式的独立性和可维护性，推荐使用module css来代替styled-components。

## 如何在webpack中配置module css？

为了启用module css的支持，需要对React项目中的webpack配置进行相应的调整。webpack是一个现代JavaScript应用程序的静态模块打包器，它通过loader和plugin的配置来转换、捆绑或打包资源和模块。以下是一个基础的webpack配置修改示例：

```javascript
module: {
  strictExportPresence: true,
  rules: [
    {
      parser: {
        requireEnsure: false
      }
    },
    {
      oneOf: [
        // ...其他规则
        {
          test: /\.css$/,
          exclude: /\.module\.css$/,
          use: getStyleLoaders({
            importLoaders: 1,
            sourceMap: isEnvProduction && shouldUseSourceMap,
          }),
          sideEffects: true,
        },
        {
          test: /\.module\.css$/,
          loader: 'style-loader!css-loader?modules',
        },
        // ...其他规则
      ],
    },
  ],
}
```

上面的配置指出，对于非module css的普通样式文件，使用getStyleLoaders函数返回的一组loader进行处理。对于module css，直接使用`style-loader`和开启了CSS模块化的`css-loader`进行处理。

需要注意的是，为了让`.module.less`文件也能生效，webpack的配置也必须做相应调整：

```javascript
{
  test: /\.module\.less$/,
  exclude: /node_modules\.(less)$/,
  use: [
    require.resolve('style-loader'),
    {
      loader: require.resolve('css-loader'),
      options: {
        importLoaders: 1,
        modules: true,
      },
    },
    {
      loader: require.resolve('postcss-loader'),
      plugins: () => [
        require('postcss-flexbugs-fixes'),
      ],
    },
    {
      loader: require.resolve('less-loader'),
      options: {
        modules: true,
        getLocalIdent: getCSSModuleLocalIdent,
      },
    },
  ],
}
```

通过这些修改，webpack可以正确地处理`.module.css`和`.module.less`文件，确保样式可以被模块化并与其他样式隔离。

## 全局声明支持module css和less

在项目的控制目录（如：`projects/control`）下增加`global.d.ts`文件，其内容为：

```typescript
declare module '*.css';
declare module '*.less';
```

这个声明文件的存在使得TypeScript能够识别以`.css`和`.less`结尾的文件模块，这对使用TypeScript开发React项目的团队来说是必要的。

## 如何在React组件中使用module css？

首先需要创建一个module css文件，例如`test.module.css`，文件内容可能如下所示：

```css
.cyan {
  border: 1px solid cyan;
}
```

然后在React组件中，通过特殊的import语法将这个样式文件导入：

```javascript
import styles from './test.module.css';
```

最后，在组件的`render`方法中，或在JSX标记中，可以使用`className`属性来应用导入的样式：

```jsx
<div className={`oldClassName ${styles.cyan}`}>This is a test div with cyan border.</div>
```

结果是，这个`<div>`将获得一个独一无二的类名，该类名对应于`test.module.css`中定义的`.cyan`样式，从而确保了样式的模块化和隔离性。

通过以上介绍，可以看出在React项目中使用module css可以实现组件样式的独立性，有效地避免了因样式冲突而导致的问题。module css的引入，提高了项目的可维护性和扩展性，对于大型的React应用而言尤为重要。