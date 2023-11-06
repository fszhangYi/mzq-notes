作为比webpack轻量级的打包解决方案，rollup可以作为深入了解**打包**这一过程原理的入门。本文将之前做过的rollup官方文档逐句翻译整理成笔记，在比较难懂的地方做了注解甚至是注例，感兴趣的小伙伴可以跟一下，由于本人水平有限，理解不到位的地方，还希望大家能够不惜赐教！

# rollup.js  
## Introduction  
### 1.Overview  
概述  
### 2.Installation  
安装  
### 3.Quick Start  
快速启动  
### 4.The Why  
为什么使用rollup？  
### 5.Tree-Shaking  
树摇  
### 6.Compatibility  
兼容性  
#### (1)Importing CommonJS  
导入CommonJS  
#### (2)Publishing ES Modules  
发布ESM  

## Overview  
### 概述  
Rollup is a module bundler for JavaScript which compiles small pieces of code into something larger and more complex, such as a library or application. It uses the new standardized format for code modules included in the ES6 revision of JavaScript, instead of previous idiosyncratic solutions such as CommonJS   
and AMD. ES modules let you freely and seamlessly combine the most useful individual functions from your favorite libraries. This will eventually be possible natively everywhere, but Rollup lets you do it today.  
  
Rollup是一个JS模块打包工具，它将碎片代码编译成整体的复杂的代码，产出自定义库或者应用程序。Rollup采用的是ES6中代码模块化的新标准，取代了之前的各种不同的解决方案，例如CommonJS和AMD。ESM的模块化方式可以使你将各个模块中对你程序有用的函数结合到一起，这个功能可能在JS以后的版本中出现，但是现在使用Rollup就可以做到这一点。  
  
## Installation  
安装  
npm install --global rollup  
This will make Rollup available as a global command line tool. You can also install it locally, see Installing Rollup locally.  
上述的代码将会使得你的Rollup成为一个全局的命令行工具，当然你也可以将其安装成为局部的工具。  
## Quick Start  
快速开始  
Rollup can be used either through a command line interface with an optional configuration file, or else through its JavaScript API. Run rollup --help to see the available options and parameters.  
Rollup可以被用作为一个命令行接口来执行rollup的配置文件，或者使用提供的js API来运行。在命令行中执行rollup --help来查看配置项和配置参数。  
See rollup-starter-lib and rollup-starter-app to see example library and application projects using Rollup  
点击查看使用rollup创建库或者应用的示例。  
These commands assume the entry point to your application is named main.js, and that you'd like all imports compiled into a single file named bundle.js.  
下面的例子默认你的应用程序的入口文件为main.js，并且你想要将所有的代码打包进入一个名为bundle.js的单一文件中。  
For browsers:  
打包成浏览器中使用的格式：使用--format iife配置  
`# compile to a <script> containing a self-executing function ('iife') ` 
`rollup main.js --file bundle.js --format iife`  
打包成node库：使用`--format cjs`配置  
For Node.js:  
`# compile to a CommonJS module ('cjs') ` 
`rollup main.js --file bundle.js --format cjs ` 
打包成浏览器和node中均可以使用的格式：使用`--format umd --name "myBundle"`  
For both browsers and Node.js:  
`# UMD format requires a bundle name  `
`rollup main.js --file bundle.js --format umd --name "myBundle"  `
为什么用两个，这实际上是设置了lib和libTarget  
## The Why  
为什么使用rollup  
Developing software is usually easier if you break your project into smaller separate pieces, since that often removes unexpected interactions and dramatically reduces the complexity of the problems you'll need to solve, and simply writing smaller projects in the first place isn't necessarily the answer. Unfortunately, JavaScript has not historically included this capability as a core feature in the language.  
This finally changed with the ES6 revision of JavaScript, which includes a syntax for importing and exporting functions and data so they can be shared between separate scripts. The specification is now fixed, but it is only implemented in modern browsers and not finalised in Node.js. Rollup allows you to write your code using the new module system, and will then compile it back down to existing supported formats such as CommonJS modules, AMD modules, and IIFE-style scripts. This means that you get to write future-proof code, and you also get the tremendous benefits of…  
在开发软件的过程中，经常将项目分割成独立的片段，这样可以去除不必要的内部交互、减少问题的复杂程度、或者方便在开始时写一些占位代码；从而使软件开发变得更加容易。遗憾的是，js在设计之初并没有考虑到将项目分割的事情。但在ES6之后，由于import和export语法的加入，使得函数和数据在分割开的js脚本间的传递成为可能。Rollup使得你在使用新的语法写完项目之后能够将其编译成当前支持的代码格式，如：CommonJs modules, AMD modules 或者IIFE格式的脚本。这意味着你可以写将来JS版本才支持的代码（因为这种代码通过rollup可以编译成现在支持的代码格式），并且rollup还将带来其他的好处。  
## Tree-Shaking  
树摇  
In addition to enabling the use of ES modules, Rollup also statically analyzes the code you are importing, and will exclude anything that isn't actually used. This allows you to build on top of existing tools and modules without adding extra dependencies or bloating the size of your project.  
使用rollup打包，除了能够支持ESM格式的代码，rollup还可以静态的分析导入的模块并自动派出没有用到的代码。这意味着打包是在现有的工具和模块上进行的从而不需要额外的依赖（指的是导入某个库中没有用得到那些方法），因此可以大大减少项目打包之后的体积。  
For example, with CommonJS, the entire tool or library must be imported.  
举例来说，如果使用的是require，则被引入的库中的所有的方法都会被导入  

```js
// import the entire utils object with CommonJS 
const utils = require('./utils');  
const query = 'Rollup';  
// use the ajax method of the utils object  
utils.ajax(`https://api.example.com?search=${query}`).then(handleResponse);  
With ES modules, instead of importing the whole utils object, we can just import the one ajax function we need:  
如果使用的是ESM模式，则无需将整个对象导入，只需要导入用到的方法即可！  
// import the ajax function with an ES6 import statement  
import { ajax } from './utils';  
const query = 'Rollup';  
// call the ajax function  
ajax(`https://api.example.com?search=${query}`).then(handleResponse);  
```
Because Rollup includes the bare minimum, it results in lighter, faster, and less complicated libraries and applications. Since this approach can utilise explicit import and export statements, it is more effective than simply running an automated minifier to detect unused variables in the compiled output code.  
正是由于rollup中包含了最少量的代码，它产出的库或者应用程序才会更轻、更快、更加的简洁；rollup利用的是EMS中显式声的import和export语句，所以它的做法比“打包完成之后再运行自动化的minifier去探测然后去除多余代码”更加有效。  
## Compatibility  
兼容性  
## Importing CommonJS  
在项目中导入CommonJS模块  
Rollup can import existing CommonJS modules through a plugin.  
Rollup通过插件来实现支持导入CommonJS模块  
## Publishing ES Modules  
发布ESM  
To make sure your ES modules are immediately usable by tools that work with CommonJS such as Node.js and webpack, you can use Rollup to compile to UMD or CommonJS format, and then point to that compiled version with the main property in your package.json file. If your package.json file also has a module field, ES-module-aware tools like Rollup and webpack 2+ will import the ES module version directly.  
为了能够使你的EMS模块代码被Node.js，webpack等使用CommonJS的工具使用；你可以将你的ESM模块代码打包成UMD格式或者CommonJS 格式，然后在package.json文件中指定版本号。如果package.json文件也是EMS格式的，那么你可以在其他ESM中直接引用package.json文件，这种做法rollup(以及webpack 2+)是支持的。