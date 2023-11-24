对于前端开发者来说，有时候也需要将开发环境下的代码部署到生产环境中去，这个时候就需要一点nginx的知识了。本文从头开始将一个崭新的CRA前端工程部署到生产环境去中。

## 1. 前端项目构建
```bash
cd C:/Users//Administrator//Documents
npx create-react-app demo --template=typescript
code ./demo
# 打包之前记得在package.json中增加、修改homepage为"."即："homepage":".",
yarn build
```

## 2. 配置nginx.conf

打开nginx配置文件nginx.conf,在http模块下新增一个服务：

```nginx
server {
  listen 80;
  server_name your_domain.com;  # 將 your_domain.com 替換為你的域名或 IP
  
  location /reminder {
      alias C:/Users/Administrator/Documents/demo/build;
      index index.html;
      try_files $uri $uri/ /reminder/index.html;
  }
}
```

## 3. 启动nginx
在nginx.exe所在的目录下面打开cmd然后执行启动命令

```cmd
nginx.exe
```

## 4. 测试
在浏览器的地址栏中输入：http://nginx所在的机器的ip地址/reminder

如果能供展示出来打包之后的效果就成功了！