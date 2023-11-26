本文将展示如何在Node.js环境中使用SQLite3进行数据库操作。我们将通过示例代码来演示如何创建数据库表、插入数据和查询操作。

安装并导入`sqlite3`模块：
```bash
yarn add sqlite3
```

```javascript
const sqlite3 = require('sqlite3');
```

### 1. 获取当前日期
定义函数`getDate`，用于获取当前日期，并返回格式化后的日期字符串：

```javascript
const getDate = () => {
    const date = new Date();
    const options = { timeZone: 'Asia/Shanghai' };
    const formattedDate = date.toLocaleString('zh-CN', options);
    const today = formattedDate.split(" ")[0].replace(/\//g, '_');
    return today;
};
```

### 2. 生成表名
函数`getName`根据当前日期生成表名，这样每天的数据都会存储在对应日期的表中：

```javascript
const getName = () => `tab_${getDate()}`;
```

### 3. 默认回调函数
定义默认的回调函数`defaultCallback`，用于处理数据库操作的成功或失败情况：

```javascript
const defaultCallback = function (err) {
    if (err) {
        console.error('执行失败：', err);
    } else {
        console.log('执行成功', this.lastID);
    }
};
```

### 4. 检查表是否存在
函数`isExist`用于检查表是否存在，并根据结果执行不同的回调函数：

```javascript
const isExist = (db, name, sucb, facb) => {
    db.get(`SELECT name FROM sqlite_master WHERE type='table' AND name='${name}'`, (err, row) => {
        if (!err && row) {
            console.log('表存在');
            sucb(db);
        } else {
            console.log('表不存在');
            facb(db);
        }
    });
};
```

### 5. 创建表
函数`newTask`用于创建新的表：

```javascript
const newTask = (db, name, cb = defaultCallback) => {
    const newTab = `CREATE TABLE ${name} (id INTEGER PRIMARY KEY AUTOINCREMENT, date TEXT, name TEXT, content TEXT, jump INTEGER)`;
    db.run(newTab, cb);
};
```

### 6. 插入数据
函数`insertTask`用于向表中插入数据：

```javascript
const insertTask = (db, name, data, cb = defaultCallback) => {
    const insert = `INSERT INTO ${name} (date, name, content, jump) VALUES (?, ?, ?, ?)`;
    db.run(insert, data, cb);
};
```

### 7. 更新数据库
函数`updateDb`用于更新数据库：

```javascript
const updateDb = (db, person, content) => {
    const name = getName();
    const data = [getDate(), person, content, 0];
    insertTask(db, name, data, () => {
        db.close();
    });
};
```

### 8. 创建数据库
函数`createDb`用于创建表，并将一条数据插入到表中：

```javascript
const createDb = (db, person, content) => {
    const name = getName();
    const data = [getDate(), person, content, 0];
    newTask(db, name, () => {
        insertTask(db, name, data, () => {
            db.close();
        });
    });
};
```

### 9. HTTP服务器
创建一个HTTP服务器，用于接收客户端的post请求，然后将post中的数据存入特定的数据库中去：

```javascript
const http = require('http');

const server = http.createServer((req, res) => {
    if (req.url === '/re_api') {
        if (req.method === 'OPTIONS') {
            res.setHeader('Access-Control-Allow-Origin', '*');
            res.setHeader('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE');
            res.setHeader('Access-Control-Allow-Headers', 'Content-Type');
            res.setHeader('Access-Control-Max-Age', '86400');
            res.end();
            return;
        } else if (req.method === 'POST') {
            let body = '';

            req.on('data', chunk => {
                body += chunk;
            });

            req.on('end', () => {
                const { content, person } = JSON.parse(body);
                const db = new sqlite3.Database('test.db');
                isExist(db, getName(), () => {
                    updateDb(db, person, content);
                }, () => {
                    createDb(db, person, content);
                });

                res.setHeader('Access-Control-Allow-Origin', '*');
                res.setHeader('Content-Type', 'text/plain; charset=utf-8');
                res.end('提交成功');
            });
            return;
        }
    }

    res.statusCode = 404;
    res.end();
});

server.listen(9000, () => {
    console.log('后端服务已启动，监听端口 9000');
});
```

以上的代码都可以放在名为server.js的文件中，当后端服务器接收到客户端的数据之后，就会把数据存到当天日期命名的表中，方便之后的数据统计。下面是完整的代码：

```js
// db
const sqlite3 = require('sqlite3');

const getDate = () => {
    // 创建一个日期对象
    const date = new Date();
    // 配置时区
    const options = { timeZone: 'Asia/Shanghai' };
    // 使用配置的时区将时间戳转化成时间字符串
    const formattedDate = date.toLocaleString('zh-CN', options);
    // 解析得到当前时间
    const today = formattedDate.split(" ")[0].replace(/\//g, '_');
    return today;
}

const getName = () => `tab_${getDate()}`;

const defaultCallback = function (err) {
    if (err) {
        console.error('执行失败：', err);
    } else {
        console.log('执行成功', this.lastID);
    }
};

const isExist = (db, name, sucb, facb) => {
    // 检查表是否存在
    db.get(`SELECT name FROM sqlite_master WHERE type='table' AND name='${name}'`, (err, row) => {
        if (!err && row) {
            console.log('表存在');
            sucb(db);
        } else {
            console.log('表不存在');
            facb(db);
        }
    });
}

const newTask = (db, name, cb = defaultCallback) => {
    const newTab = `CREATE TABLE ${name} (id INTEGER PRIMARY KEY AUTOINCREMENT, date TEXT, name TEXT, content TEXT, jump INTEGER)`;
    db.run(newTab, cb);
};

const insertTask = (db, name, data, cb = defaultCallback) => {
    const insert = `INSERT INTO ${name} (date, name, content, jump) VALUES (?, ?, ?, ?)`;
    db.run(insert, data, cb);
}


const updateDb = (db, person, content) => {
    const name = getName();
    const data = [getDate(), person, content, 0];
    insertTask(db, name, data, () => {
        db.close();
    });
}

const createDb = (db, person, content) => {
    const name = getName();
    const data = [getDate(), person, content, 0];
    newTask(db, name, () => {
        insertTask(db, name, data, () => {
            db.close();
        });
    })
}

// server
const http = require('http');

const server = http.createServer((req, res) => {
    console.log('req.url: ', req.url);
    console.log('req.method: ', req.method);
    if (req.url === '/re_api') {
        if (req.method === 'OPTIONS') {
            // 处理预检请求
            res.setHeader('Access-Control-Allow-Origin', '*');
            res.setHeader('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE');
            res.setHeader('Access-Control-Allow-Headers', 'Content-Type');
            res.setHeader('Access-Control-Max-Age', '86400');
            res.end();
            return;
        }
        else if (req.method === 'POST') {
            let body = '';

            req.on('data', chunk => {
                body += chunk;
            });

            req.on('end', () => {
                console.log('请求体:', body);
                const { content, person } = JSON.parse(body);
                const db = new sqlite3.Database('test.db');
                isExist(db, getName(), () => {
                    updateDb(db, person, content);
                }, () => {
                    createDb(db, person, content);
                });

                res.setHeader('Access-Control-Allow-Origin', '*');
                res.setHeader('Content-Type', 'text/plain; charset=utf-8'); // 添加字符编码
                res.end('提交成功');
            });
            return;
        }
    }

    // 没有匹配的路径，返回 404
    res.statusCode = 404;
    res.end();
});

server.listen(9000, () => {
    console.log('后端服务已启动，监听端口 9000');
});

```
