# umi脚手架文件作用解析
使用`npx create-umi`可以方便的创建出一个umi项目，本文就使用此脚手架生成项目的各个文件做以解析；通过阐述脚手架中的各个文件及其作用，能够对umi创建的项目有更深的理解。

## 0. node_modules/
存放项目依赖包和可执行脚本的目录。

## 1. .editorconfig
此文件用来提供编辑器相关的设置；因为不同的开发习惯使用的编辑器可能是不同的，所以需要此文件来统一代码风格。例如，缩进是4个空格还是2个空格等。

## 2. gitignore
此文件用来告诉git那些文件不会被管理。

## 3. .npmrc
npm的配置文件，一般来说会配置registry的地址，例如：

`registry=https://registry.npmjs.org/`

需要注意的是，如果registry的值不为`https://registry.npmjs.org/`, 那么使用`npm login`或者`npm publish`是会失败的！

## 4. .umirc.ts
umi项目的主配置文件，配置路由、mock等重要信息，是umi项目中最重要的文件。

## 5. package.json
项目中的包管理文件，重要性不言而喻。

## 6. tsconfig.json
typescript相关的配置文件，如设置规则等。

## 7. typings.d.ts
tsconfig.json文件的支持文件。

## 8. yarn.lock
用来锁定项目中使用的依赖的版本，以及这些依赖相互依赖的版本的文件。

## 9. src/
src目录下面存放相关的资源文件

### 9.1 .umi/
缓存文件的目录
### 9.2 assets/
存放图片等资源的目录
### 9.3 layouts/
存放布局文件相关的目录。由index.less和index.tsx组成，**不是必须的**。
### 9.4. pages/
存放各个页面的目录，每一个页面放在一个单独的文件夹下面，这个文件夹中：放有index.tsx以此来提供页面组件；放有index.less来提供样式。

# 总结
在这些文件中，最为重要的是`.umirc.ts`和`src/pages/`。

当然`package.json`也非常重要，但是其重要性不只体现在umi项目中，而是对于所有项目都很重要。

所以，后面会着重介绍`.umirc.ts`和`src/pages/`中的内容。