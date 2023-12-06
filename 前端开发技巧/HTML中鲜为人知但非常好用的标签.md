在万维网的每一个角落里，HTML标签构建着互联网界界面的骨架。随着HTML5的推出，许多新的、功能丰富的标签被引入，但仍有不少标签鲜为人知。本文旨在揭露那些隐藏在广阔HTML规范中的宝藏标签，每一个都各怀绝技，等待着开发者的探索。

以下是15个鲜为人知但非常实用的HTML标签，每个标签都附带详细说明和示例代码，以展示其功能与魅力。

### 1. `<details>` 和 `<summary>`
`<details>`标签用于创建一个可折叠的内容区域，而`<summary>`标签则为此内容提供了一个可见的标题。

```html
<details>
  <summary>更多信息</summary>
  这里可以包含各种详细内容，当点击标题“更多信息”时，内容会展开显示。
</details>
```

### 2. `<figure>` 和 `<figcaption>`
`<figure>`标签用来标识独立的内容（如图表、照片、代码等），而`<figcaption>`用来提供说明性文本。

```html
<figure>
  <img src="image.jpg" alt="风景图片">
  <figcaption>图1: 应用中的风景画面示例</figcaption>
</figure>
```

### 3. `<mark>`
`<mark>`标签用于表示文本内的高亮部分，通常用在搜索结果等场合。

```html
<p>在此示例中，<mark>高亮</mark>文本引起读者关注。</p>
```

### 4. `<time>`
`<time>`标签定义日期或时间，使之更容易被搜索引擎和其他服务解析。

```html
<article>
  发布时间：<time datetime="2021-01-01">2021年1月1日</time>
</article>
```

### 5. `<meter>`
`<meter>`标签用于表示已知范围或分数内的标量值（如磁盘使用情况）。

```html
<meter value="2" min="0" max="10">2 out of 10</meter>
```

### 6. `<progress>`
`<progress>`标签用于显示任何类型任务的进度。

```html
<progress value="70" max="100"></progress>
```

### 7. `<output>`
`<output>`标签用于表示表单计算的结果。

```html
<form oninput="result.value=parseInt(a.value)+parseInt(b.value)">
  <input type="range" id="a" value="50">+
  <input type="number" id="b" value="50">
  <output name="result" for="a b">100</output>
</form>
```

### 8. `<datalist>`
`<datalist>`标签与input元素搭配使用，提供输入字段的预定义选项。

```html
<input list="browsers" name="browser" id="browser">
<datalist id="browsers">
  <option value="Edge">
  <option value="Firefox">
  <option value="Chrome">
  <option value="Opera">
  <option value="Safari">
</datalist>
```

### 9. `<keygen>`
`<keygen>`标签用于生成一对公开和私有的密钥，主要用于表单中确保数据安全。

```html
<form action="submit_key.php" method="post">
  用户名: <input type="text" name="usr_name">
  密钥: <keygen name="security">
  <input type="submit">
</form>
```

### 10. `<bdi>`
`<bdi>`标签用于隔离文本，从而避免受周围文本的布局影响，特别是在涉及到RTL（从右到左书写）的文本时。

```html
<p>在用户名 <bdi>user@#$%</bdi> 中，BDI标签确保特殊字符不会影响布局。</p>
```

### 11. `<ruby>`, `<rt>` 和 `<rp>`
这三个标签配合使用，`<ruby>`用于表示注音或字符注解，`<rt>`用来提供注音，而`<rp>`为了在不支持ruby标签的浏览器中提供括号。

```html
<ruby>
  漢 <rt> ㄏㄢˋ </rt>
  <rp>(</rp><rt>Han</rt><rp>)</rp>
</ruby>
```

### 12. `<template>`
`<template>`标签用于容纳客户端会话期间不显示，但可能在后面用JavaScript实例化的HTML。

```html
<template id="template">
  <p>Hello, World!</p>
</template>
```

### 13. `<dialog>`
`<dialog>`标签用来定义一个对话框或窗口。

```html
<dialog open>
  <p>欢迎信息</p>
  <button>确认</button>
</dialog>
```

### 14. `<data>`
`<data>`标签连接了给定内容和机器可读的翻译，通常用来更新客户端的数据。

```html
<ul>
  <li><data value="21053">MVP</data></li>
  <li><data value="21054">新秀</data></li>
</ul>
```

### 15. `<wbr>`
`<wbr>`（word break opportunity，单词断开处的机会）表示在长单词或连字符内的偏好断点。

```html
<p>这个非常非常非常长的单词是由众多字母组成的：<br>
super<wbr>cali<wbr>fragilistic<wbr>expiali<wbr>docious</p>
```

通过精心选择和运用以上标签，可以在网页设计中取得独特效果，并提高内容的语义化和可读性。这些标签不仅表明了HTML不断发展的脚步，也反映了标准的逐渐完善。在构建现代化网站和应用程序的过程中，这些鲜为人知的HTML标签将作为强有力的助手。