闲来无事，使用nodejs中的``模块中的``方法仿写pm2构建了一个简单的进程管理器（SimplePM），本文记录了仿写过程中的重要步骤：

1. **构建启动方法（start）：** 在 `start` 方法中，我们使用 `child_process` 模块的 `spawn` 方法来启动一个新的 Node.js 进程。我们将脚本名称作为参数传递给 `spawn` 方法，并将返回的子进程对象存储在 `this.processes` 对象中。然后，我们使用子进程对象的事件处理程序来监听子进程的输出和关闭事件。
2. **构建停止方法（stop）：** 在 `stop` 方法中，我们检查指定脚本名称是否存在于 `this.processes` 对象中，如果存在则调用 `kill` 方法杀死对应的子进程。然后从 `this.processes` 对象中删除对应的脚本。
3. **构建重启方法（restart）：** 在 `restart` 方法中，我们首先调用 `stop` 方法停止指定脚本的运行，然后调用 `start` 方法重新启动该脚本。这样就实现了重启功能。
4. **构建暂停方法（pause）：** 在 `pause` 方法中，我们检查指定脚本名称是否存在于 `this.processes` 对象中，如果存在则向子进程发送 `SIGSTOP` 信号以暂停其执行。这将使子进程暂时停止，直到接收到 `SIGCONT` 信号恢复执行。
5. **构建恢复方法（resume）：** 在 `resume` 方法中，我们检查指定脚本名称是否存在于 `this.processes` 对象中，如果存在则向子进程发送 `SIGCONT` 信号以恢复其执行。
6. **构建列表方法（list）：** 在 `list` 方法中，我们使用 `console.log` 打印当前被管理的脚本。

下面是完整的 `SimplePM` 类的代码：


# SimplePM

`SimplePM` 是一个用于管理Node.js应用程序的简单进程管理器。

## 构造函数

```javascript
const { spawn } = require('child_process');

class SimplePM {
  constructor() {
    this.processes = {};
  }
```

### 启动方法（start）

```javascript
  start(script) {
    // 使用子进程模块的 spawn 方法启动新的 Node.js 进程
    const proc = spawn('node', [script]);

    // 将子进程对象存储在 processes 对象中
    this.processes[script] = proc;

    console.log(`Started ${script} with pid: ${proc.pid}`);

    // 监听子进程的输出（stdout）
    proc.stdout.on('data', (data) => {
      console.log(`${script} stdout:\n${data}`);
    });

    // 监听子进程的错误输出（stderr）
    proc.stderr.on('data', (data) => {
      console.error(`${script} stderr:\n${data}`);
    });

    // 监听子进程的关闭事件
    proc.on('close', (code) => {
      console.log(`${script} process exited with code ${code}`);
      delete this.processes[script];
    });
  }
```

### 停止方法（stop）

```javascript
  stop(script) {
    // 检查指定脚本是否存在于 processes 对象中
    if (!this.processes[script]) {
      console.log(`No such process for script: ${script}`);
      return;
    }

    console.log(`Stopping ${script}`);
    // 杀死对应的子进程
    this.processes[script].kill();

    // 从 processes 对象中删除对应的脚本
    delete this.processes[script];
  }
```

### 重启方法（restart）

```javascript
  restart(script) {
    // 先停止指定脚本，然后重新启动它
    this.stop(script);
    this.start(script);
  }
```

### 暂停方法（pause）

```javascript
  pause(script) {
    // 检查指定脚本是否存在于 processes 对象中
    if (!this.processes[script]) {
      console.log(`No such process for script: ${script}`);
      return;
    }

    console.log(`Pausing ${script}`);
    // 向子进程发送 pause 信号暂停执行
    this.processes[script].stdin.write('pause\n');
    // unix中：this.processes[script].kill('SIGSTOP');
  }
```

### 恢复方法（resume）

```javascript
  resume(script) {
    // 检查指定脚本是否存在于 processes 对象中
    if (!this.processes[script]) {
      console.log(`No such process for script: ${script}`);
      return;
    }

    console.log(`Resuming ${script}`);
    // 向子进程发送 resume 信号恢复执行
    this.processes[script].stdin.write('resume\n');
    // unix中：this.processes[script].kill('SIGCONT');
  }
```

### 列表方法（list）

```javascript
  list() {
    console.log('Currently managed scripts: ', Object.keys(this.processes));
  }
}

module.exports = SimplePM;
```

## SimplePM的完整代码
```js
// simplePM.js
const {
  spawn
} = require('child_process');

class SimplePM {
  constructor() {
    this.processes = {};
  }

  start(script) {
    // 使用子进程模块的 spawn 方法启动新的 Node.js 进程
    const proc = spawn('node', [script]);

    // 将子进程对象存储在 processes 对象中
    this.processes[script] = proc;

    console.log(`Started ${script} with pid: ${proc.pid}`);

    // 监听子进程的输出（stdout）
    proc.stdout.on('data', (data) => {
      console.log(`${script} stdout:\n${data}`);
    });

    // 监听子进程的错误输出（stderr）
    proc.stderr.on('data', (data) => {
      console.error(`${script} stderr:\n${data}`);
    });

    // 监听子进程的关闭事件
    proc.on('close', (code) => {
      console.log(`${script} process exited with code ${code}`);
      delete this.processes[script];
    });
  }

  stop(script) {
    // 检查指定脚本是否存在于 processes 对象中
    if (!this.processes[script]) {
      console.log(`No such process for script: ${script}`);
      return;
    }

    console.log(`Stopping ${script}`);
    // 杀死对应的子进程
    this.processes[script].kill();

    // 从 processes 对象中删除对应的脚本
    delete this.processes[script];
  }

  restart(script) {
    // 先停止指定脚本，然后重新启动它
    this.stop(script);
    this.start(script);
  }

  pause(script) {
    // 检查指定脚本是否存在于 processes 对象中
    if (!this.processes[script]) {
      console.log(`No such process for script: ${script}`);
      return;
    }

    console.log(`Pausing ${script}`);
    // 向子进程发送 SIGSTOP 信号暂停执行
    // this.processes[script].kill('SIGSTOP');
    // this.processes[script].stdin.pause(); // 只能通过信号的方式控制子进程的启停
    this.processes[script].stdin.write('pause\n');
  }

  resume(script) {
    // 检查指定脚本是否存在于 processes 对象中
    if (!this.processes[script]) {
      console.log(`No such process for script: ${script}`);
      return;
    }

    console.log(`Resuming ${script}`);
    // 向子进程发送 SIGCONT 信号恢复执行
    this.processes[script].stdin.write('resume\n');
    // this.processes[script].stdin.resume(); // 只能通过信号的方式控制子进程的启停
    // this.processes[script].kill('SIGCONT');
  }
  list() {
    console.log('Currently managed scripts: ', Object.keys(this.processes));
  }
};

// 进程管理器的实例化
const pm = new SimplePM();
// 使用子进程启动
pm.start('./hello.js');
// 罗列管理的进程
pm.list();
// 三秒之后暂停子进程
setTimeout(()=>{
  pm.pause('./hello.js');
  // 再过三秒重新启动子进程
  setTimeout(()=>{
    pm.resume('./hello.js');
  }, 3000);
}, 3000);
```

## 测试代码
```js
// hello.js
const fs = require('fs');

process.stdin.setEncoding('utf-8');

// 监听来自父进程的输入
process.stdin.on('data', (data) => {
  const message = data.trim();

  if (message === 'pause') {
    // 执行暂停操作
    // 可以在此处添加相关逻辑
    fs.appendFileSync('./log.txt', '子进程已暂停\n');
  } else if (message === 'resume') {
    // 执行恢复操作
    // 可以在此处添加相关逻辑
    fs.appendFileSync('./log.txt', '子进程已恢复\n');
  }
});

// 写一个回调地狱
setTimeout(() => {
  fs.appendFileSync('./log.txt', 'hello1\n');
  setTimeout(() => {
    fs.appendFileSync('./log.txt', 'hello2\n');
    setTimeout(() => {
      fs.appendFileSync('./log.txt', 'hello3\n');
      setTimeout(() => {
        fs.appendFileSync('./log.txt', 'hello4\n');
        setTimeout(() => {
          fs.appendFileSync('./log.txt', 'hello5\n');
          setTimeout(() => {
            fs.appendFileSync('./log.txt', 'hello6\n');
            setTimeout(() => {
              fs.appendFileSync('./log.txt', 'hello7\n');
            }, 1000);
          }, 1000);
        }, 1000);
      }, 1000);
    }, 1000);
  }, 1000);
}, 1000);
```


