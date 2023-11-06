让我们书接上文：
## Command Line Interface
命令行接口
## Configuration Files
配置文件
## Config Intellisense
配置感知
## Differences to the JavaScript API
与js API的区别
## Loading a configuration from a Node package
从node库中加载一个配置文件
## Caveats when using native Node ES modules
使用原生NODE ESM的注意事项
## Getting the current directory
获取当前的目录
## Importing package.json
导入package.json文件
## Command line flags
### 命令行标识
- -h/--help
- -p <plugin>, --plugin <plugin>
- --configPlugin <plugin>
- --bundleConfigAsCjs
- -v/--version
- -w/--watch
- --silent
- --failAfterWarnings
- --environment <values>
- --waitForBundleInput
- --stdin=ext
- --no-stdin
- --watch.onStart <cmd>, --watch.onBundleStart <cmd>, --watch.onBundleEnd <cmd>, --watch.onEnd <cmd>, --watch.onError <cmd>
### Reading a file from stdin
通过stdin读取文件
    
Rollup should typically be used from the command line. You can provide an optional Rollup configuration file to simplify command line usage and enable advanced Rollup functionality.

典型的使用rollup的方法是使用命令行的方式，但是你可以配置一个rollup的配置文件来使用更加高级的方法。
### Configuration Files
配置文件

    Rollup configuration files are optional, but they are powerful and convenient and thus recommended. A config file is an ES module that exports a default object with the desired options:

    Rollup配置文件并不是必要的，但是它却是强大且便利的。推荐使用rollup的配置文件。配置文件本身是一个eEMS，它将配置以export default的方式暴露出来，如下所示：
```js
    export default {
  input: 'src/main.js',
  output: {
    file: 'bundle.js',
    format: 'cjs'
  }
};
```
Typically, it is called rollup.config.js or rollup.config.mjs and sits in the root directory of your project. Unless the --configPlugin or --bundleConfigAsCjs options are used, Rollup will directly use Node to import the file. Note that there are some caveats when using native Node ES modules as Rollup will observe Node ESM semantics.
    
配置文件一般命名为rollup.config.js或者rollup.config.mjs并且存在于项目的根目录下。除非在使用rollup的时候在命令行上加上 --configPlugin或者 --bundleConfigAsCjs，否则rollup将会自动用node导入配置文件。由于rollup监视了原生node ESM的语义，所以在使用原生node ESM的时候会有一些警告！
    
If you want to write your configuration as a CommonJS module using require and module.exports, you should change the file extension to .cjs.
    
如果你想将你的配置文件以CommonJS的方式导入导出，那么只需要将文件的后缀名称改成cjs即可！ 
    
You can also use other languages for your configuration files like TypeScript. To do that, install a corresponding Rollup plugin like @rollup/plugin-typescript and use the --configPlugin option:
    
`rollup --config rollup.config.ts --configPlugin typescript`
    
如果你想使用其他非js语言来编写你的配置文件（例如ts），那么你需要引入相关的rollup插件作为依赖；如果你使用的是ts语言，那么你需要安装@rollup/plugin-typescript插件，并且在命令行中使用--configPlugin设置：
    
`rollup --config rollup.config.ts --configPlugin typescript`
    
Using the `--configPlugin` option will always force your config file to be transpiled to CommonJS first. Also have a look at Config Intellisense for more ways to use TypeScript typings in your config files.
    
但是一旦使用了诸如--configPlugin这样的设置，那么你的配置文件将会强行被解读为CommonJS格式的。在Config Intellisense中有更多的使用ts作为配置文件的做法。
    
Config files support the options listed below. Consult the big list of options for details on each option:
    
下面罗列了配置文件中的所有配置项，在big list of options中可以找到每一项的具体含义。
```js
// rollup.config.js
// can be an array (for multiple inputs)
// 可以是一个数组，如果入口有多个的话
export default {
  // core input options
  external,
  input, // conditionally required
  plugins,

  // advanced input options
  cache,
  onwarn,
  preserveEntrySignatures,
  strictDeprecations,

  // danger zone
  acorn,
  acornInjectPlugins,
  context,
  moduleContext,
  preserveSymlinks,
  shimMissingExports,
  treeshake,

  // experimental
  experimentalCacheExpiry,
  perf,

  // required (can be an array, for multiple outputs)
  output: {
    // core output options
    dir,
    file,
    format, // required
    globals,
    name,
    plugins,

    // advanced output options
    assetFileNames,
    banner,
    chunkFileNames,
    compact,
    entryFileNames,
    extend,
    footer,
    hoistTransitiveImports,
    inlineDynamicImports,
    interop,
    intro,
    manualChunks,
    minifyInternalExports,
    outro,
    paths,
    preserveModules,
    preserveModulesRoot,
    sourcemap,
    sourcemapBaseUrl,
    sourcemapExcludeSources,
    sourcemapFile,
    sourcemapPathTransform,
    validate,

    // danger zone
    amd,
    esModule,
    exports,
    externalLiveBindings,
    freeze,
    indent,
    namespaceToStringTag,
    noConflict,
    preferConst,
    sanitizeFileName,
    strict,
    systemNullSetters
  },

  watch: {
    buildDelay,
    chokidar,
    clearScreen,
    skipWrite,
    exclude,
    include
  }
};
```
    
You can export an array from your config file to build bundles from several unrelated inputs at once, even in watch mode. To build different bundles with the same input, you supply an array of output options for each input:
    
你可以从配置文件中导出一个数组（上面的示例是导出一个对象），这样的话你可以一次打包好几个无关的输入，甚至同时监控多个打包过程。如果你想通过一个统一的入口打包出几份不同的代码，那么只需要将output的值设置成为一个数组即可！

```js
// rollup.config.js (building more than one bundle)
// 下面是一次打包好几个入口的示例
export default [
  {
    input: 'main-a.js',
    output: {
      file: 'dist/bundle-a.js',
      format: 'cjs'
    }
  },
// 这个是一个入口，多分打包代码的例子
  {
    input: 'main-b.js',
    output: [
      {
        file: 'dist/bundle-b1.js',
        format: 'cjs'
      },
      {
        file: 'dist/bundle-b2.js',
        format: 'es'
      }
    ]
  }
];
```
    
If you want to create your config asynchronously, Rollup can also handle a Promise which resolves to an object or an array.
    
如果你想通过异步创建配置文件，那么rollup将会返回一个用Promise包裹的对象或者数组（对应配置文件中暴露出来的是一个对象或者数组）
```js
// rollup.config.js
// 注意这里export default返回的是被Promis包裹的对象，而不是之前的对象或者数组
import fetch from 'node-fetch';
export default fetch('/some-remote-service-or-file-which-returns-actual-config');
```
    
Similarly, you can do this as well:
    
同样，下面的做法将会返回一个被Promise包裹的数组
```js
// rollup.config.js (Promise resolving an array)
export default Promise.all([fetch('get-config-1'), fetch('get-config-2')]);
```
To use Rollup with a configuration file, pass the `--config` or `-c` flags:
    
在命令行中指明此次rollup打包需要使用配置文件的做法是使用--config配置。--config后面跟着的是配置文件的地址，如果此项为缺省值，那么会自动以下面的顺序在根目录中寻找配置文件：rollup.config.mjs  ->  rollup.config.cjs  ->  rollup.config.js

// pass a custom config file location to Rollup
`rollup --config my.config.js`

if you do not pass a file name, Rollup will try to load# configuration files in the following order: rollup.config.mjs -> rollup.config.cjs -> rollup.config.js

`rollup --config`

You can also export a function that returns any of the above configuration formats. This function will be passed the current command line arguments so that you can dynamically adapt your configuration to respect e.g. --silent. You can even define your own command line options if you prefix them with config:

配置文件不仅可以导出一个对象、数组、或者由Promise包裹的对象或者数组，它还可以返回一个函数，只要这个函数的返回值是一个对象或者数组，就如同最初返回的值那样。如果配置文件返回的是一个函数，那么在命令行中是可以传入这个函数运行时的所需参数的。
```js
// rollup.config.js
// 如下所示：在rollup.config.js文件中暴露出来的是一个函数，这个函数会随着形参值得变化返回不同的配置对象或者配置对象数组
import defaultConfig from './rollup.default.config.js';
import debugConfig from './rollup.debug.config.js';
export default commandLineArgs => {
  if (commandLineArgs.configDebug === true) {
    return debugConfig;
  }
  return defaultConfig;
};
```
If you now run rollup --config --configDebug, the debug configuration will be used.

这里需要注意的是配置文件中暴露出的函数是自动被调用执行的，并且执行的时候configDebug的值会被传递给commandLineArgs函数作为入参。

By default, command line arguments will always override the respective values exported from a config file. 

默认情况下，命令行中传递的参数一定会覆盖由配置文件暴露出的相应值。

If you want to change this behaviour, you can make Rollup ignore command line arguments by deleting them from the commandLineArgs object:
如果你想要取消这个覆盖机制，你可以参考如下的示例：
```js
// rollup.config.js
export default commandLineArgs => {
  const inputBase = commandLineArgs.input || 'main.js';

  // this will make Rollup ignore the CLI argument
// 这样做之后从命令行中传递过来的参数就被忽略掉了
  delete commandLineArgs.input;
  return {
    input: 'src/entries/' + inputBase,
    output: {...}
  }
}
```
## Config Intellisense
配置ts智能提示

Since Rollup ships with TypeScript typings, you can leverage your IDE's Intellisense with JSDoc type hints:

由于rollup采用ts智能类型提示，所以你可以使用JSDoc类型提示来激活IDE的智能类型提示功能。
```ts
// rollup.config.js/**
 * @type {import('rollup').RollupOptions}
 */const config = {
  /* your config */
};
export default config;
```

Alternatively you can use the defineConfig helper, which should provide Intellisense without the need for JSDoc annotations:

或者你可以使用defineConfig 帮助，defineConfig 会提供智能感知，这个时候就无须使用JSDoc annotations了。
```js
// rollup.config.js
import { defineConfig } from 'rollup';
export default defineConfig({
  /* your config */
});
```

Besides RollupOptions and the defineConfig helper that encapsulates this type, the following types can prove useful as well:

除了RollupOptions 和defineConfig helper 来概括这些类型，下面的这些配置既强大又有用。

- OutputOptions: The output part of a config file
- OutputOptions：输出侧的配置文件
- Plugin: A plugin object that provides a name and some hooks. All hooks are fully typed to aid in plugin development.
- Plugin：一个插件对象可以提供插件的名称和钩子函数。在插件的开发过程中，所有的钩子已经被完全了键入。
- PluginImpl: A function that maps an options object to a plugin object. Most public Rollup plugins follow this pattern.
- PluginImpl：本质上是一个函数，它可以将配置对象映射成为一个插件对象。绝大多数rollup的插件遵循这种模式。
    
    
You can also directly write your config in TypeScript via the `--configPlugin` option. With TypeScript, you can import the RollupOptions type directly:

你可以用ts只写写配置文件，只要你在使用rollup命令行的时候配置--configPlugin。使用ts书写配置文件的好处在于，你可以直接导入rollup的配置类型作为提示：
```js
import type { RollupOptions } from 'rollup';
const config: RollupOptions = {
  /* your config */
};
export default config;
```