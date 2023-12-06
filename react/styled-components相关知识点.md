# styled-components相关知识点
这篇文章整理和前端库styled-components相关的一些知识点作为笔记记录，方便使用react开发的各位老爷和自己今后的查阅和复用。

## 1. styled层级关系：
下面的例子中div表示的是MyItem的子元素，样式设置在MyItem>div上而不是MyItem上：
```tsx
MyItem = styled(Item)`
		div{
			width: 180px;
			overflow: hidden;
			white-space: nowrap;
			text-overflow: ellipsis;
			display: inline-block;
		}
`
```
## 2. 设置自己的样式：不需要加任何的其他修饰！直接在模板字符串中写样式即可！
```tsx
MyItem = styled(Item)`
		border: 1px solid black;
`
```
## 3. 设置自身的悬浮样式：使用class
```tsx
	MySubmenu = styled(Submenu)`
		.react-contexify.react-contexify__submenu:hover {
			overflow-y: auto ;
		}
`
```
## 4. 设置悬浮样式：使用&&
```tsx
    MySubmenu = styled(Submenu)`
		&&::after{
			content: url(${require('../../assets/images/more.png').default});
			position: absolute;
			right: 10px;
			top: 10px
		}
      &&:hover::after{
			content: url(${require('../../assets/images/multi.png').default});
		}
	`;
```

- `&&:hover::after`的书写顺序；url的引入方式；切换content的内容。
- 在这个特定的代码片段中，`&&::after和&::after`是等效的，因为它们都指代相同的元素（即父组件）并添加伪元素选择器`::after`。
- 在`styled-components`中，&&的作用是增加样式的特异性，以确保其覆盖较低级别的样式规则。
- 而`&`是一个特殊的占位符，代表当前组件本身。在大多数情况下，&可以直接省略，因为`styled-components`会自动将样式应用于组件本身。
- 因此可以直接写成`::after`而不使用&&或&。在大多数情况下，`::after`选择器将应用于父元素（组件）并创建伪元素。
- 在`styled-components`中，默认情况下，样式会自动应用到组件本身，所以`::after`选择器将适用于该组件。
- 但是，在某些特定情况下，如果其他样式规则的优先级较高并且可能影响到`::after`选择器的样式，你可以使用`&&::after`来增加特异性，确保这个样式规则生效。
- 否则，在绝大多数情况下，直接使用`::after`就足够了。
- 所以，在这个例子中，`&&::after和&::after`实际上是相同的，但为了确保样式的特异性，使用`&&::after`可以提供更强的保证。

## 5. 可以使用styled来去掉封装好的组件中的某些不需要的内容：比如去掉不喜欢的箭头：
```tsx
	MySubmenu = styled(Submenu)`
		.react-contexify__submenu-arrow{
			display: none;
	`;
```
## 6. 附加一个小技巧：图片资源的引入方式：对比以下两种方式：
```tsx
content: url(${require('../../assets/images/multi.png').default}); 
content: url('../../assets/images/multi.png');
```
第一种方式一定不会在打包之后找不到资源，因为这个时候已经变成base64了（被打包时候的相应插件处理过了）；
第二种方式可能会找不到！例如在浏览器中的地址就变成了：
- `url('../../assets/images/more.png')`完全按照写的来了；但是这个地址指向的位置却是：		`http://localhost:8888/assets/images/more.png`

与此相关的知识点：
- 在生产环境中，如果某个图片的资源路径是url()形式，其根路径通常是相对于部署该应用程序的服务器或域名的根目录。
- 具体来说，根路径取决于你的应用程序是如何部署和访问的。
- 例如，如果你的应用程序部署在域名为example.com的服务器上，根路径可能是`http://example.com/`。
- 如果你的应用程序部署在子目录中，根路径可能是`http://example.com/myapp/`，其中myapp是应用程序的子目录名称。