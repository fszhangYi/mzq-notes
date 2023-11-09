# .umirc.ts文件内容解析
作为umi框架中最重要的文件之一，`.umirc.ts`中提供的是一个配置对象；了解此对象的各个key的作用是至关重要的，本文先叙述了`.umirc.ts`文件的基本框架，然后对暴露出去的配置对象中的常见key逐个介绍。

## 1. .umirc.ts文件的基本框架
```typescript
import { defineConfig } from "umi";

export default defineConfig({
    routes: [
        {path: "/", component: "index"},
        {path: "/docs", component: "docs"},
    ],
    npmClient: "yarn",
    metas: [
        {
            name: "version", 
            content: "1.0.0",
        }
    ],
})
```

## 2. 配置对象中的其他重要字段

### 2.1 routes
用来定义应用的路由配置，是一个数组：`Record<'path'|'components', string >`

### 2.2 hash
是否给url添加一个哈希路由

### 2.3 dynamicImport
将应用配置成动态导入的行为
#### 2.3.1 dynamicImport.loading
如果导入的耗时时间长就显示这个字段配置的组件
### 2.4 ignoreMomentLocale: boolean
针对第三方库moment的优化字段
### 2.5 proxy
自带的代理，例如：
```ts
"/api/v1": {
    target: "http://192.168.1.94:9999",
    changeOrigin: true,
}
```
### 2.6 meta
为入口文件，即index.html中注入meta信息：
```ts
metas: [
    {name: "version", content: "1.0.0"},
]
```
### 2.7 chainWebpack
可以在此字段中覆盖一些webpack的配置：
```ts
chainWebpack: (config, {webpack}) => {
    config.merge({...})
}
```

举个实际的例子，用来修改webpack的**optimization**配置：
```ts
  chainWebpack: (config, { webpack }) => {
    config.merge({
      optimization: {
        splitChunks: {
          cacheGroups: {
            commons: {
              name: 'commons',
              chunks: 'initial',
              minChunks: 2,
            },
          },
        },
      },
    });
  },
```

### 2.8 authAllowed
用来指定哪些页面不需要权限
```ts
authAllowed: ['/download'],
```

### 2.9 根目录页面
```ts
    path: '/',
    component: '@/pages/welcome/index.jsx',
```