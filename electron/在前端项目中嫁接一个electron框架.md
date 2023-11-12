# 在react项目中嫁接一个electron框架
通常使用create-react-app开发的前端项目用在Browser端，而本文介绍了一种能够快速将B端应用程序快速套接在electron中从而实现在Client端打开的方式，主要步骤如下：

- 1. 创建react项目：
**必须执行yarn eject，否则electron builder工作的时候会被cra预置项影响**
```shell
npx create-react-app my-electron --template=typescript
cd my-electron
git init && git add . && git commit -m "feat: project init" && yarn eject -y
code .
```

- 2. 安装electron依赖
**注意版本号一定要锁定**
```shell
yarn add electron@^21.3.1 electron-builder@^23.6.0 -D
yarn add cross-env@7.0.3 winreg@1.2.4
```

- 3. 在package.json中增加/合并下面的代码
    - **description repository author不写的话会警告**
    - **main homepage不写的话会报错**
    - **build.asar的值一定要是false**
    - **yarn edist中需要注意的是将打包好的build目录放到src下面去，这样才会有读取的权限**
```json
  "description": "ElectronDemo",
  "repository": "http://****.git",
  "author": "tom",
  "license": "MIT",
  "main": "index.js",
  "homepage": ".",
  "scripts": {
    "estart": "cross-env NODE_ENV=development electron .",
    "epack": "electron-builder --dir",
    "edist": "yarn build && rm -rf src/build && cp -rf build src/build && electron-builder"
  },
  "build": {
    "appId": "com.electron.app.TestElectron",
    "productName": "test-electron",
    "directories": {
      "output": "../client"
    },
    "extraResources": [],
    "asar": false,
    "win": {
      "icon": "./icon/logo.ico",
      "target": "dir"
    },
    "linux": {
      "target": "dir"
    }
  },
```

- 4. 在项目根目录下创建名为index.js的文件，然后在其中填入下面的内容：
**这个文件会被默认成为estart和edist的入口文件**
```touch index.js```

```javascript
const { app, BrowserWindow:BW, globalShortcut, screen } = require('electron');
const path = require("path");
// 注意这里的src/build和yarn edist是对应的
const APP_URL = path.resolve(__dirname, './src/build/index.html'); 

let mainWin = null;

const registerShortcut = () => {
    globalShortcut.register('ctrl+r', () => BW.getFocusedWindow()?.webContents?.reload());
    globalShortcut.register('ctrl+shift+i', () => BW.getFocusedWindow()?.webContents?.openDevTools({mode: 'right'}));
};

const createWindow = (screen, preloadPath, url=APP_URL) => {
  const win = new BW({
    x: screen.bounds.x + 50,
    y: screen.bounds.y + 50,
    frame: true,         // 隐藏标题栏等窗口边框
    kiosk: false,          // 全屏模式
    fullscreen: false,     // 全屏模式
    resizable: true,     // 不可改变窗口大小
    minimizable: true,   // 禁止最小化
    transparent: false,    // 窗口背景透明
    webPreferences: {
      contentSecurityPolicy: "default-src 'self'",
      preload: path.join(__dirname, preloadPath), // 预加载的脚本文件路径
    }
  });

  win.maximize()
  // if (process.env.NODE_ENV === 'development') {
  //   win.setIcon(path.join(__dirname, "./build/logo192.png"));
  // } else {
  //   win.setIcon(path.join(process.resourcesPath, "./icon/logo.png"));
  // }
  win.setBackgroundColor('#fff')
  // win.loadURL(url)
  win.loadFile(APP_URL);
  return win;
};

app.whenReady().then(() => {
  mainWin = createWindow(screen.getPrimaryDisplay(), "./preload/primary.js");
  registerShortcut();
  app.on('activate', () => BW.getAllWindows().length || (mainWin = createWindow()));
});

app.on('window-all-closed', () => process.platform !== 'darwin' && app.quit());

app.on('quit', () => globalShortcut.unregisterAll());
```

- 5. 整理结构
**primary中什么都可以不用写，但是这个结构需要在，否则index.js加载预置项的时候会报错**
```shell
mkdir preload && touch preload/primary.js
```

- 6. 处理白屏问题
打包之后的react代码中静态资源路径不正确需要手动修改
否则直接使用yarn estart会出现白屏
解决方案：如果路由采用的时hash路由，或者根本不需要考虑路由，则只需要在package.json中增加
```"homepage": ".",```
就可以解决问题了

- 7. 设置安全策略，在public/的index.html中增加一行
```html
    <meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self'">
```

- 8. 设置electron打包镜像源
**一共是七个**
```shell
yarn config set registry  http://****
yarn config set SASS_BINART_SITE http://****
yarn config set PYTHON_MIRROR http://****
yarn config set disturl http://****
yarn config set ELECTRON_MIRROR http://****
yarn config set CHROMEDRIVER_CDNURL http://****
yarn config set ELECTRON_BUILDER_BINARIES_MIRROR http://****
```

- 9. 打包成应用程序
```yarn edist```

- 10. 日常开发
```yarn estart```