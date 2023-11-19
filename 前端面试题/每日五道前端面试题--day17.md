以下题目来自掘金等其它博客，但是问题的答案都是根据笔者自己的理解做出的。如果你最近想要换工作或者巩固一下自己的前端知识基础，不妨和我一起参与到每日刷题的过程中来，如何？

第17天要刷的面试题如下：
1. audio和video标签用法及常用属性
2. source标签的作用
3. h5中新增的表单类型有哪些
4. h5中新增的表单属性和事件有哪些
5. h5中移除了哪些元素 

下面是我的一些理解：

## 1. audio和video标签用法及常用属性
```html
<audio src='' controls autoplay loop='true'></audio>
```
其中：
- src表示的是资源的地址
- controls表示的是播放控件
- autoplay表示自动播放
- loop表示循环播放


```html
<video src='' poster='imgs/x.png' controls></video>
```
其中：
poster表示视频的封面，默认为第一帧。

## 2. source标签的作用
source标签卓为video的子元素使用。其作用为兼容不同浏览器对于video使用的资源格式的支持力度不同：
```html
<video>
    <source src='_.flv' type='video/flv'></source>
    <source src='_.mp4' type='video/mp4'></source>
</video>
```
可以看出，对于source标签而言，src属性和type属性比较重要！

## 3. h5中新增的表单类型有哪些
h5中新增的表单类型有：
- email
- url
- number
- search
- range
- color
- time
- date
- datetime
- datetime-local
- week
- month

# 4. h5中新增的表单元属性和事件有哪些？
- 新增的表单属性：placeholder autofocus autocomplete(="om"/"off") required pattern multiple form
- 新增的表单事件：oninput oninvalid

# 5. h5中删除了哪些标签
- 删除了原来**纯以表现为目的的元素**：`basefont big center font s strike tt u`
- 删除了一些可能**对可用性产生负面影响的元素**：`frame frameset noframes`