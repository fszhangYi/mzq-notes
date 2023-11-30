## 搭建Vue组件库的步骤
本文介绍了搭建一个vue组件库的基本步骤，为了让读者能够成功复现，我将所使用的package.json文件放在了文章最后，以防止版本不同造成的错误。

### 1. 创建项目
首先创建一个项目文件夹，并使用`npm init vue`命令初始化Vue项目。

```shell
mkdir component
cd component
npm install create-vue -g
npm init vue
# 设置项目名称为demo
# 构建完了之后执行
cd demo
code ./
```

### 2. 构建组件库
在项目根目录下，创建一个`package`文件夹，用于存放组件库代码。在`package`文件夹内创建`myui`文件夹作为组件库的主目录。创建`index.js`文件作为组件库的入口文件。

```shell
mkdir -p package/myui
touch package/myui/index.js
echo export default {test:123} > package/myui/index.js
```

### 3. 配置workspaces
在项目的`package.json`文件中，添加`workspaces`字段连接工作空间。确保Node.js版本在16以上，以支持workspaces字段的配置。

```json
"workspaces": [
  "package/*"
]
```

### 4. 初始化组件库包
进入`package/myui`目录，使用`npm init -y`命令初始化组件库的包。确保`package/myui/package.json`文件中的`main`字段的值为`index.js`。

```shell
cd package/myui
npm init -y
cd ../../
```

### 5. 编写打包脚本
在项目根目录下执行`npm install`命令安装依赖。创建`lib.config.js`文件，配置组件库的打包脚本。

```javascript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  build: {
    lib: {
      entry: "./package/myui/index.js",
      name: "myui"
    },
    outDir: "package/myui/lib",
  },
  plugins: [
    vue(),
  ],
})
```

demo项目的package.json中新增运行脚本：
```bash
"build:ui" : "vite build --config lib.config.js",
```

### 6. 引入并调试组件库
在项目的`src/main.js`文件中引入`myui`组件库，并在浏览器控制台输出相关内容，以测试引入和调试组件库。

```javascript
import * as Myui from "myui";
console.log(Myui.default.test);
```

### 7. 创建实例组件
在`myui`中创建两个示例组件，用于展示和测试组件库的功能。
```bash
cd package/myui
mkdir HelloWorld TheWelcome
touch HelloWorld/HelloWorld.vue TheWelcome/TheWelcome.vue
cd ../../
```

HelloWorld.vue的内容为：
```vue
<script setup>
defineProps({
  msg: {
    type: String,
    required: true
  }
})
</script>

<template>
  <div class="greetings">
    <h1 class="green">{{ msg }}</h1>
    <h3>
        Hello
    </h3>
  </div>
</template>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.greetings h1,
.greetings h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}
</style>

```
TheWelcome.vue的内容为：
```vue
<script setup>
defineProps({
  msg: {
    type: String,
    required: true
  }
})
</script>

<template>
  <div class="welcoming">
    <h1 class="green">{{ msg }}</h1>
    <h3>
        Welcome
    </h3>
  </div>
</template>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.welcoming h1,
.welcoming h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .welcoming h1,
  .welcoming h3 {
    text-align: left;
  }
}
</style>

```

### 8. 编写组件库入口代码
在组件库的`index.js`文件中，将所有组件挂载到Vue实例上，实现全局注册和按需引入功能。

```javascript
import HelloWorld from './HelloWorld/HelloWorld.vue';
import TheWelcome from './TheWelcome/TheWelcome.vue';

let components = {
    HelloWorld,
    TheWelcome,
};

// 全局加载
export default {
    install(vue){
        Object.keys(components).forEach(key => {
            vue.component(key, components[key])
        })
    }
}

// 按需引入
export {
    HelloWorld,
    TheWelcome,
};
```

### 9. 验证打包
执行`npm run build:ui`命令验证生成组件库的打包文件是否成功。如果成功则会在package/myui中生成一个lib文件夹，其中的文件有：

demo.mjs  demo.umd.js  favicon.ico  style.css

第一个是module格式的用于工程引入，第二个是umd格式的可以用在浏览器中用script标签引入

### 10. 打包demo项目
执行`npm run build`命令打包demo项目，生成组件库的说明文档。

### 11. 发布组件库
进入`package/myui`目录，使用`npm login`和`npm publish`命令登录并发布组件库。

以上就是搭建一个Vue组件库的基本步骤。

## 附录
demo的package.json文件的内容
```json
{
  "name": "demo",
  "version": "0.0.0",
  "private": true,
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "build:ui" : "vite build --config lib.config.js",
    "preview": "vite preview",
    "lint": "eslint . --ext .vue,.js,.jsx,.cjs,.mjs --fix --ignore-path .gitignore",
    "format": "prettier --write src/"
  },
  "dependencies": {
    "pinia": "^2.1.7",
    "vue": "^3.3.4"
  },
  "devDependencies": {
    "@rushstack/eslint-patch": "^1.3.3",
    "@vitejs/plugin-vue": "^4.4.0",
    "@vue/eslint-config-prettier": "^8.0.0",
    "eslint": "^8.49.0",
    "eslint-plugin-vue": "^9.17.0",
    "prettier": "^3.0.3",
    "vite": "^4.4.11"
  },
  "workspaces": [
    "package/*"
  ]
}
```