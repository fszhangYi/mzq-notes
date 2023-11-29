# Nexus私有库发包流程

在软件开发中，经常会使用到各种依赖包来实现项目的功能。而这些依赖包有时候需要从公有库中下载，有时候则需要从私有库中发布。本文将针对如何使用Nexus私有库进行包的发布进行详细的介绍和实践，帮助开发者更好地管理和发布自己的包。

## 1. 安装依赖

首先，在使用Nexus私有库进行包的发布之前，需要安装一些工具和依赖包。在命令行中执行以下命令来安装这些依赖：

```bash
npm install -D @release-it/conventional-changelog@^2.0.1 release-it@^14.6.2
npm install -D npm-check-updates@^11.8.3 simple-git@^2.42.0 shelljs@^0.8.5
```

这些依赖包将用于后续的发布流程中。

## 2. 修改package.json中的配置

接下来，需要修改项目的`package.json`文件，以配置一些必要的内容。需要添加一些脚本命令和发布配置。

首先，在`scripts`字段中添加以下脚本命令：

```json
"scripts": {
  "release": "npm run depVerUp && npm install && release-it",
  "release-dry-run": "release-it --dry-run",
  "depVerUp": "node depVerUpgrade.js"
},
```

这些脚本命令将帮助进行包的发布和版本升级。

然后，在`publishConfig`字段中添加以下发布配置：

```json
"publishConfig": {
  "access": "public",
  "registry": "http://localhost:9999/repository/npm-private/",
  "directory": "."
},
```

这些配置用于指定包的发布权限和发布的私有库的地址。

## 3. 创建发布配套文件

为了能够顺利地发布包，需要创建一些配套的文件来进行辅助操作。在命令行中执行以下命令来创建这些文件：

```bash
touch depVerUpgrade.js .release-it.cjs
```

然后，需要在这些文件中填入相应的内容。

### depVerUpgrade.js

`depVerUpgrade.js`文件的内容如下：

```javascript
/**
通过扫描项目的 package.json 文件, 检查和更新 npm 项目依赖包的工具。
特点和功能：
1. 检查依赖包的最新版本
2. 显示更新信息
3. 自定义更新策略"~"、"^"、"*"
4. 将更新后的依赖版本号直接写入到 package.json
5. 支持锁定文件（如 package-lock.json）
6. 支持npm、yarn、pnpm
 */
const ncu = require('npm-check-updates');

// 定义要升级的包列表，包括以'package1/'和'package2/'开头的所有包
const upgradePackageList = [
  'package1/*',
  'package2/*',
];

ncu.run({
  upgrade: true,
  silent: true,
  packageManager: 'yarn',
  filter: upgradePackageList,
  packageFile: './package.json',
}).then((upgraded) => {
  console.log(`当前最新版本：`, upgraded);
});
```

`depVerUpgrade.js`文件是一个通过扫描项目的`package.json`文件，来检查和更新项目依赖包的工具。运行这个文件可以获取最新版本的依赖包信息。

### .release-it.cjs

`.release-it.cjs`文件的内容如下：

```javascript
module.exports = {
  hooks: {
    "after:git:release": "git push origin HEAD"
  },
  git: {
    commitMessage: "chore: release branch release ${version}",
    requireCommits: true
  },
  plugins: {
    "@release-it/conventional-changelog": {
      infile: "CHANGELOG.md",
      preset: {
        name: "conventionalcommits",
        types: [{
            type: "feat",
            section: "Features"
          },
          {
            type: "fix",
            section: "Bug Fixes"
          },
          {
            type: "docs",
            section: "Docs"
          },
          {
            type: "refactor",
            section: "Refactors"
          },
          {
            type: "style",
            section: "Refactors"
          },
          {
            type: "perf",
            section: "Performances"
          },
          {
            type: "chore",
            section: "Miscellaneous"
          },
          {
            type: "build",
            section: "Miscellaneous"
          },
          {
            type: "test",
            section: "Miscellaneous"
          }
        ]
      }
    }
  }
};
```

`.release-it.cjs`文件是一个配置文件，用于指定发布过程中的一些钩子和插件的配置。这些配置将影响发布的流程和生成的CHANGELOG文件。

## 4. 切换至发布包的私有域下

在发布包之前，需要将npm的registry切换至私有域下。在命令行中执行以下命令：

```bash
npm config set registry http://localhost:9999/repository/npm-private/
```

这样，就可以使用私有域来进行包的发布。

## 5. 登录

在发布之前，需要先登录到私有域。在命令行中执行以下命令：

```bash
npm login
```

然后，输入账号、密码和邮箱进行登录。这样，就可以进行包的发布了。

## 6. 发布

在进行包的发布之前，可以先运行以下命令进行一次dry-run，以便了解发布流程和可能的影响：

```bash
npm run release-dry-run
```

如果一切正常，可以进行实际的包发布。执行以下命令：

```bash
npm run release
```

这个命令将会触发脚本命令`release`，然后进行包的发布流程。

## 7. 撤销发布

如果需要撤销之前的发布，可以执行以下命令：

```bash
npm unpublish test-release-it@1.0.4 -f
```

这个命令将会撤销之前发布的`test-release-it`包的版本`1.0.4`。

## 8. 登出

在包发布完成之后，可以执行以下命令来登出私有域，保护账号安全：

```bash
npm logout
```

## 9. 切换回公有域

最后，在包发布完成之后，可以将npm的registry切换回公有域，以便后续项目的正常使用。执行以下命令：

```bash
npm config set registry http://localhost:9999/repository/npm-group/
```

这样，包发布流程就完成了。

## 包中增加自定义命令

有时候，需要在包中增加一些自定义的命令，以方便开发者使用。下面是一个示例：

首先，需要创建一个`bin`目录，并在其中创建一个`go.js`文件。执行以下命令：

```bash
mkdir bin
touch bin/go.js
```

然后，将以下内容写入`go.js`文件中：

```javascript
#!/usr/bin/env node

console.log('go away!!!!');
```

这段代码定义了一个打印"go away!!!!"的命令。

接下来，在`package.json`中增加以下字段来连接这个命令：

```json
"bin": {
  "go": "./bin/go.js"
},
```

这样，就可以在命令行中执行`go`命令来触发这个自定义命令了。

然后，执行以下命令将这个包链接到全局：

```bash
npm link
```

这样，就可以在命令行中直接执行自定义的`go`命令了。

如果想测试一下这个自定义命令的效果，可以创建一个新的项目，并在其中执行以下命令：

```bash
mkdir raw-test && cd raw-test && yarn init -y && code . && exit
```

这个命令将在当前目录下创建一个新的项目，并打开编辑器。

为了避免干扰，需要在执行完测试后取消全局链接。执行以下命令：

```bash
npm unlink test-release-it
```

这样，就可以在测试项目中安装这个包，并执行自定义的`go`命令了：

```bash
yarn add -D test-release-it
npx test-release-it go
```

尽管使用`npx`命令执行自定义命令效果相同，但在全局链接的情况下会更方便和高效。

## 声明包过时

如果某个包不再维护，可以在`package.json`中添加`deprecated`字段来声明该包已过时。只需在该字段中提供一条提示信息即可：

```json
"deprecated": "This package is no longer maintained. Please use the new-package instead."
```

这样，当用户通过npm安装或更新包时，将会得到该过时警告信息。

## 总结
本文对如何使用Nexus私有库发包的过程进行了详细的说明和实践。通过以上步骤，可以方便地发布和管理自己的包，并有效地提高开发效率。