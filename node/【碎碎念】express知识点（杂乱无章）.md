## 1. jwt

### 作用
用来在express中做**鉴权**的！

### 相比于session
session解决的问题是跨域认证的问题，其大致的步骤为：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/409484ed822a4211bea25bc4b058ab3d~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1210&h=298&s=434457&e=png&b=fdfcfc)
session解决方案的最大问题在于难以在集群服务器中及时的共享。session也有将数据持久化的方案，比如写入数据库或者别的持久层，但是这种方案的工程量比较大，并且风险比较高，如果挂掉则单点登录（所谓单点登录就是如果A,B网站是同一家公司的产品，在用户在A网站登录之后就无需在B网站登录第二次）就会失败。

所以服务器端索性就不保存数据了，所有数据都由客户端自己保存。

举个形象一点的例子：你去超市需要把自己的东西放到储物箱里，储物箱是超市提供了，就相当于session；假如你家附近有两家华润万家，你先去了第一家将东西存了进去(超市会给你一个小片其实就是cookie)，然后又去了第二家，那么你能从第二家超市取出自己存的东西吗？既然取不出来那你进入第一家超市的时候超市就给你发了一个袋子，你把你的东西装进袋子就可以了，这样你在购物的时候自己拎着这个袋子，如果你想去第二个超市，拿着这个袋子也是可以直接进去的。这就是session和jwt解决方案的不同。
### 字符串的组成
jwt字符串中间没有换行而是通过两个点将其分成了三个部分，这三个部分分别是：头部、负载和签名，对应的英文名称分别为：header, paylaod 和 signature。或者直接理解成：`HEader.Payload.Signature`

- 其中signature是对前两个部分进行签名的，也就是防止内容被篡改；既然需要签名那必然涉及到加密算法和密钥：其中采用的加密算法被写到了header中（默认为SHA 256），而密钥则保存在服务器中；签名可以简单的看成通过下面的公式得到：
`HMACSHA256(A+'.'+B,C)`
其中，
- A: base64UrlEncode(header)
- B: base64UrlEncode(payload)
- C: secret

### header
header是一个json对象形如：
```json
{
    "alg": "HS256",
    "typ": "JWT",
}
```
其中的alg表示的就是加密算法的名称而typ表示的是令牌的类型，当然值就是JWT了。

### 安装jwt
```shell
npm install jsonwebtoken
```

### jwt在客户端
在客户端，jwt可以放在localStorage中，或者放在cookie中；如果放在cookie中会有跨域的问题，因此可以放在post的请求体中，或者作为请求头；一般来说此请求头的名称为：`Authorization: Bearer <jwt>`

### 服务端生成jwt的同步、异步方式

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9498618b894f409baecf85243c0cd527~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=676&h=284&s=116960&e=png&b=f5f7fb)

### 尝试网站
https://jwt.io

### payload中的字段
- iss: 签发人
- exp: 过期时间
- sub: 主题
- aud: 受众
- nbf: 生效时间
- iat: 签发时间
- jti: 编号

jwt默认是不会进行加密的，所以你可以在其中放一些信息，但是不要将秘密信息放在这个里，除非是经过加密的信息。

## 2. util.promisify的使用
此方法可以将一个具有回调函数的函数转成promise格式的，如下所示：
```js
const { promisify } = require('util');
const fs = require('fs');
const readFile = promisify(fs.readFile); // 相当于是readFileSync

exports.getDb = async () => {
    const data =  await readFile('dbPath', 'utf8');
    return JSON.parse(data);
}
```

Customer:
```js
const express = require('express');
const fs = require('fs');

const { getDb } = require('./db');
const app = express();
```

### promisify结合jwt上的方法

<p align=center><img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f26f2ef7c519402aae80f5dce4e8eab0~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=561&h=250&s=169666&e=png&b=2b2a37" alt="image.png"  /></p>

## 3. API中的过滤参数
一般来说服务器提供的信息不可能一次性全部给客户端，因此客户端在请求数据的时候就要提供过滤参数，这些过滤参数存在于查询字符串中，不属于api的一部分，但是可以通过req.query获得：

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/29d1616a08974b0c99023c33f23f74be~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=819&h=226&s=172717&e=png&b=fdfdfd)
需要注意的是，在大型项目中，由于复杂性很高，因此查询某个特定信息的方式可能不只有一种，而这是被允许的！

## 4. 常见的响应码
### 总共100多种可以分成五大类
1. 1XX: 相关信息
2. 2XX: 操作成功
3. 3XX: 重定向
4. 4XX: 客户端错误
5. 5XX: 服务器错误

### 常用响应码
- 200: Ok get请求成功
- 201: Created 创建成功
- 202: Accepted 异步排队
- 204: No Content 删除成功
- 400: Invalid Request 请求错误服务器不响应
- 401：Unauthorized 未登录
- 403：Forbidden 登录但无权限
- 404：Not Found 未找到资源
- 406：Not Acceptable 无索取格式
- 410：Gone 资源永久消失
- 422：Unprocesable entity 不可操作实体
- 500: Internal Server Error 服务器内部错误

### 搭配
- 修改数据成功201，修改失败500或403
- 删除数据的时候的404 204和500搭配：

![57.jpg](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/690485d5d26c4c7b9cae7211dd33cbd6~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1042&h=626&s=297706&e=jpg&b=2b2b37)

- 新增时候payload格式错误：422

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/60b0529fc0674dc788e7c73c7b5d8d24~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=595&h=187&s=107322&e=png&b=2a2a37)

### Attention
The best practice is respond with an accurate status code, that is very important!

what is a bad practice:

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e4fc3ae650bb48c49705b8cfaf5ff56a~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=996&h=435&s=189365&e=png&b=faf8fb)

In the picture above, status code 200 is not compare to the content because an error token in it.

And what is a good one?

respond with status code 400 which means wrong query data format.

## 5. 数据库查询及失败处理示例

![6.jpg](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bf3212deb34e46379b571e261214b52d~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1012&h=211&s=44603&e=jpg&b=2b2b37)

## 6. 面向切面开发 -- AOP
就是在现有代码中，在程序的生命周期或者横向流程中加入、减去一个或者多个功能，而不影响原来的功能。

而AOP编程的思想体现在Express中就是其最大特色：也就是形形色色的中间件的使用，事实上，一个Express应用的创建就是各个中间件一起努力的结果。

中间件函数的作用：
- 执行任何代码
- 修改res或者req对象
- 结束请求的周期
- 调用下一个中间件


![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8cf76ba9901f4ee69ee4faa6a2ea0ffb~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=730&h=363&s=200528&e=png&b=fbfafd)

显然，路由本身完全符合AOP概念，所以路由处理函数本身也是中间件，也可以使用next函数：
```js
app.get('/', (req.res.next)=>{
    next();
    // res.send('get /');
})
```

### 常用的中间件
- express.json
- express.urlencoded
```js
app.use(express.urlencoded()); // postman->post->body->x-www-form-urlencoded
```
- morgan
```js
const express = require('express');
const morgan = require('morgan');

const app = express();

app.use(morgan('tiny'));

app.get('/', (req, res) => {
    res.send('Hello World!');
});

app.listen(3000, ()=>{});
```

- cors
- express.raw
- express.text
- express.static


<p align=center><img src="https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/372456c7afb141f2a48361dbe48b2737~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=612&h=682&s=519418&e=png&b=fdfdfd" alt="image.png" width="50%" /></p>

## 7. express-validator中间件
网址： https://express-validator.github.io
### 方法validationResult的使用
此方法直接作用于req对象，如果之前检验有失败的情况，则会全部记录在返回的记录中：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/03957ab3fd5a431db8063161f68217fd~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=464&h=233&s=108018&e=png&b=2c2b37)

### 多个校验
router.post或其他路由的middleware中间件位置可以放置一个数组，这个数组中的每一个元素都可以是一个校验函数，于是，当需要对post的payload的user对象进行内容校验的时候，就可以这样做：
- 1. 引入相关的依赖：


![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6196142759fc4b2aa47c34fa1be765a3~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1139&h=56&s=98806&e=png&b=2c2a37)
- 2. 对payload的内容进行校验：


![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/eb32fefc3eb94ac1a0766dc022279c0e~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1224&h=636&s=807990&e=png&b=2b2a37)

### 其他校验方案

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/43262323f08a4c69ac4201d52f346dfb~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=731&h=179&s=153084&e=png&b=fefcfc)
## 8. 创建一个用户
通过接口创建一个新的用户的时候，调用User方法产生一个新的实例，在产生实例的时候会自动将post携带的信息写入到数据库中，数据库会返回生成的信息，这个时候需要将密码从返回信息中特别的删掉，以防止其被返回到浏览器中去：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7f4999bd9dbf4b358868439a95ab643d~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=274&h=63&s=23810&e=png&b=2b2937)

这里可以用lodash中的omit方法而不是delete。

去除数据验证的骨架为：

![27.jpg](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c30bcbf7d24746f0811b216fc49d0909~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=720&h=529&s=138771&e=jpg&b=2b2b37)

## 9. express中使用路由匹配的一些例子：
也就是说路由可以是正则表达式，而不仅是字符串
- `/a/`
- `/.*fly$/`
- `/ab(cd)?e`
- `/ab?cd`
- `/ab*cd`
- `/random.text`
- `'/users/:userId(\\d+)'`
express内部使用的是名为：path-to-regexp这个第三方库
**强调！查询字符串不属于路由路径的一部分**

## 10. express中的路由组织架构
```js
const express = require('express');
const router = express.Router();
router.use(require('./user'));
router.use('/profiles', require('./profiles'))

module.exports = router;
```

### 如何给路由限定访问前缀
```js
app.use('/abc', router);
```

## 11. 避免同步代码
尽量避免在服务器中写同步代码

## 12. express的返回代码 -- 成功和失败的情况 500 200
```js
fs.readFile('./db.json','utf8',(err,data)=>{
    if (err) {
        return res.status(500).json({error: err.message})
    }
    
    const db = JSON.parse(data);
    res.status(200).json(db.post);
})
```

## 13. app上的route方法
使用app.route可以以链式的方式往同一个个路由地址上挂载不同method的处理函数，这体现了restful接口规范的应用：

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5502a0137581480fb69fcee892753e7b~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=450&h=309&s=223700&e=png&b=e4f0f6)

### 另一个符合restful的方法 -- app.all
使用app.all可以为同一个路由的不同method加上相同的处理逻辑，如下图所示：

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/386789f7591c4426a3d0308c0c936317~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=628&h=116&s=97938&e=png&b=faf8f6)

## 14. 数据校验的过程
一般说校验数据分为三个部分：1. 格式校验 2. 业务逻辑校验 3. 凭证校验；而如果使用的是express-validator则可以使用req这个不变量作为三个步骤中数据交换的载体；下面的代码是一个修改内容的示例，首先验证了用户名和密码的格式是否正确，然后验证了用户的存在性，最后验证了密码的正确性；在这个过程中无需多次查询数据库，因为可以将查询的结果挂载在req这个对象上供后续校验使用：


![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/294e67d437f048e69583cbba9bd8f466~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=877&h=769&s=522270&e=png&b=2a2a37)

## 15. 路径参数
路由参数被命名为URL段，用来捕获URL中在其位置处指定的值。捕获的值将会填充到req.params对象中，并将路径中指定的route参数的名称作为其各自的键：

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/55d1c65656fe40d6816e71d1bd19b0ed~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=276&h=64&s=29642&e=png&b=2b2a36)

## 16. res上的send end write对比
在 Express.js 服务器（构建于 Node.js 的 HTTP 模块之上）中，`res.send`、`res.end` 和 `res.write` 是用来处理服务器返回给客户端的响应的方法。每个方法有稍微不同的用途：

1. **res.send()**
   - 这是一个 Express.js 方法，用于向客户端发送各种类型的响应。它可以处理对象、数组、字符串和缓冲区。如果传递对象或数组，它将以正确的内容类型（`application/json`）发送 JSON 响应。如果发送字符串，它将根据字符串发送 HTML 或纯文本，并相应地设置内容类型（如果包含 HTML 标签，则为 `text/html`，否则为 `text/plain`）。
   - 它还处理结束响应的任务，因此在 `res.send()` 之后不需要调用 `res.end()`。

2. **res.end()**
   - 这个方法来自 Node.js 的原生 HTTP 模块。它向服务器表示所有的响应头和正文已经发送，服务器应该认为这个消息已完成。它用于快速结束响应而不带任何数据。如果您需要在响应结束时发送数据（并且您不使用 `res.send`），您将使用 `res.end(data)`。

3. **res.write()**
   - 同样来自 Node.js 的 HTTP 模块，`res.write()` 用于发送响应正文的块。这可以多次调用，以提供正文的连续部分，这对于流式传输大量数据或在处理数据时发送数据非常有用。但是，在使用 `res.write()` 发送完数据后，必须调用 `res.end()` 来完成响应。

总结：
- `res.send()` 是一个更高级别、功能更丰富的 Express.js 方法。它可以发送各种类型的内容，并为您结束响应。
- `res.end()` 是一个来自 Node.js 的低级别方法，用于结束响应过程。
- `res.write()` 用于分部分流式传输响应，必须跟随 `res.end()` 来完成响应。

## 17. 一个典型的node api server的架构

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f91ae238180445e09b8381c669793a0b~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=495&h=352&s=120440&e=png&b=f9f9f8)

## 18. 路由匹配中的：鉴权、Error、404

![25.jpg](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c4f64c48a2f2431196af1048142c07eb~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=574&h=525&s=179179&e=jpg&b=2b2b37)

### 中间件处理Error的原理
使用中间件处理路由过程的原理就在于express中对于next函数的特殊规定：
- 如果将除了'route'之外的任何内容作为形参传入到next()函数中去，express框架就会将此形参视为Error交给错误处理中间件并无视除了错误处理中间件之外的其他中间件。
- 而如果什么都不传的话，就会进入到下一个路由中去。
- 对于错误处理中间件来说，其本质上和其他中间件函数一样，但特别之处就是形参的数目为4，也就是说它是使用形参的数目做重载的，因此4个参数的这个count必须被保证；并在所有的中间件之后再使用：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f3f90be0d2c24be7905dad0fa9b85d79~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=492&h=218&s=132912&e=png&b=2a2936)

### Attention
如果你想要将error中的信息发送给前端，那么下面这种做法是不正确的：

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d5fbe52c377549bf8d77a845456aa444~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=396&h=185&s=67683&e=png&b=2b2a37)

原因在于，msg等信息是挂载在error对象的原型属性上的，所以JSON对其序列化的时候是不会包含这些属性值的，解决方案就是使用util中的format方法：

## 19. 用户登录接口的设计原理
- 1. 获取请求体数据
- 2. 对数据进行基本验证
- 3. 对数据进行业务验证
- 4. 验证通过将数据存储在数据库
- 5. 通知前端操作成功

### 用户注册接口的设计原理

```js
router.post('/users', [
    body('user.username').notEmpty().withMessage('username required'),
    body('user.password').notEmpty().withMessage('password required'),
    body('user.email').notEmpty().withMessage('email required').bail().custom(
        async email => {
            const user = await User.findOne({ email });
            if(user) {
                return Promise.reject('user has been exist.')
            }
        }
    ),
], (req, res, next) => {
    const errors = validationResult(req);
    if(!errors.isEmpty()) {
        return res.status(400).json(
            {
                errors: errors.array()
            }
        )
    }

    next();
}, userCtrl.register)
```

数据流图如下：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5292149c935c4ded93cb69c99ee71520~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1074&h=977&s=66797&e=png&b=ffffff)

## 20. res对象上面的常用方法
- download
- end
- json
- jsonp
- redirect
- render
- send
- sendFile
- sendStatus

## 21. 客户端保存用户密码的逻辑
服务器收到用户的上传密码之后先拼接前缀，然后使用MD5直接加密，最后保存在数据库中。这样就算数据库被盗取了，那么也只能看到加密之后的密码，拿不到明文。此外由于md5的加密是单向的，这就意味着服务器也不知道明文是什么，只能通过用户再次输入正确的明文之后走相同的加密流程，将结果和数据库中的储存值进行比对方可知道密码是否正确。也就是说，在密码写入数据库之后通过服务器破解密码明文是不可能的，因为服务器这个时候只能判断密码对不对而不能判断密码是什么。
```js
// mkdir -p util/md5.js
const crypto = require('crypto');

module.exports = str => {
    return crypto.createHash('md5').update('prefix'+str).digest('hex');
}
```

## 22. 关于Api的具体形式
- 如果服务器要提供的数据量很大或者业务非常复杂，则推荐使用专门的域名：`https://api.example.com`
- 如果不是的话就可以做成下面这种样子：`/api/*/**`

## 23. 关于Api的版本管理
同样对应有：
- `https://api.example.com/v1`
- `/api/v1/*/**`
- 或者作为头信息放入报文中，但是不直观。

## 24. res的cookie方法使用举例
```js
res.cookie('foo', 'bar');
res.cookie('a', 123);
res.status(201).send('ok');
```

## 25. 一个简单的获取数据库中信息的接口的构建示例

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0b9074ce68684d9096cb827bcfaabd05~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=430&h=387&s=189242&e=png&b=2a2936)

## 26. cors中间件的使用
如果使用了cors这个中间件，那么非常明显的一个点就在于，在响应报文中会出现`Access-Control-Allow-Origin: *`,如下图所示：

![47.jpg](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a81601d76f6743cc96326c9f5a800f9c~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=2532&h=1170&s=585174&e=jpg&b=2b2b37)

## 27. mongoose.Schema配置写入时自动对密码进行md5加密
Thanks to the Schema function mongoose provided, we can md5 our password automatically!

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b3bf71fbdb6f4a72b3146ed760e3aac7~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=437&h=684&s=223205&e=png&b=2b2a37)

不仅如此，由于密码是比较敏感的数据，我们还可以设置查询的时候默认情况下不要将其作为查询结果返回：

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d44f71ade43c4dcaac3a96682d87abc0~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=295&h=144&s=54638&e=png&b=282632)

这样的话使用`findOne`方法就无法将`password`这个数据读取出来了；那么有时候确实需要将其选出来，这个时候只需要额外执行一步就可以了：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4312c79e4c7949b1baeea4cfb14d4162~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=811&h=38&s=55144&e=png&b=2c2a37)

## 28. RESTFUL

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ea0d4020edc04acb839c3b6c45e7d4da~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1502&h=519&s=621733&e=png&b=fefdfd)