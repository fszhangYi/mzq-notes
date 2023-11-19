# CRA生成的index.html相关知识
作为前端开发工程师，如果采用React开发前端工程，我们常常使用create-react-app创建项目架构。使用CRA打包得到的前端项目中包含index.html文件，本文对此文件中的一些细节做了剖析，总结index.html中不被注意的技术细节。

## 1. index.html中包含的script脚本的平稳退化
```html
<noscript>You need to enable javascript to run this app. </noscript>
```
在用户使用的浏览器不支持或者用户手动禁用了javascript脚本功能的时候，提示用户升级浏览器或者关闭对脚本的禁用。

## 2. 对样式引入的处理
在react的组件中通过类似`import "./index.css"`等方式引入样式文件的时候，通过webpack的打包处理，最终呈现在index.html中的是`<head>`容器标签中的一个`<style></style>`标签。通常来说这个新增的style标签位于head的最后。

## 3. index.html文件中是如何加载打包之后的`bundle.js`的
```html
<script defer src="/static/js/bundle.js"></script>
```
可以看出来，webpack打包之后的bundle.js将会以**defer**的方式放在一个script标签中进行加载，同样这个标签也是放在`<head>`标签的底部。

## 4. 如果在项目中使用了antd等组件库，那么组件库使用到的样式文件是以什么样的方式加载的
如果项目中使用了诸如antd之类的组件库，那么该组件库会将自己依赖的一些样式文件，以`<style>`标签的方式插入到index.html文件中的`<head>`标签中。不过需要注意的是，组件库的样式文件的优先级低于用户定制，所以这些自动插入的`<style>`标签的位置是`<head>`标签的顶部，这一点和用户自定义的样式文件的位置是不同的。

## 5. 如何在index.html文件中设置此网页的图标
使用`<head>`标签所包裹的link标签可以实现对网页图标的指定：
```html
<link rel="icon" href="/favico.ico">
```
需要注意的是link是一个单标签，而对于单标签而言，是没有结束标签的，并且可以使用`/`对标签进行封闭或者不进行封闭都是可以的。

如果是在移动端，那么可能用到的是：
```html
<link rel="apple-touch-icon" href="/logo192.png" />
```

## 6.manifest的作用
在index.html文档内容中还可以看到：
```html
<link rel="manifest" href="/manifest.json">
```
对于manifest文件的作用，直接参考官方的定义即可：
*manifest.json provides metadata used when your web app is installed on a user's mobile device sor desktop.*

总结来说，就是提供应用的元信息的。

## 7. 如何设置文档的解析语言
文档通过html标签告诉浏览器自己使用的语言，比如说如果此文档的语言为英语：
```html
<html lang="en">

</html>
```

## 8. 关于index.html文件中的`%PUBLIC_URL%`
这个是占位符，其真实值会在webpack打包的时候由特定的插件处理。具体来说，webpack中处理这个占位符的插件为`InterpolateHTMLPlugin和HTMLWebpackPlugin`:
```js
plugins: [
    ...
    new InterpolateHTMLPlugin(HTMLWebpackPlugin, env.raw),
]
```
其中，env.raw的格式如下：
```js
{
    NODE_ENV: 'production',
    PUBLIC_URL: '.',
    FAST_REFRESH: true,
}
```
而`PUBLIC_URL`的值实际上是由`ppackage.json中的homepage; process.end.PUBLIC_URL; process.env.NODE_ENV`三个部分共同决定的！