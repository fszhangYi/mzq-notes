在前端开发过程中，常常遇到这样的需求：

1. 如果文字长度超出了父组件就显示省略号，并且此时出现tooltip
2. 如果文字高度超出了父组件就显示view more，否则不显示

诸如此类问题。其相同的特点就是需要在css引擎计算完毕之后才确定要不要显示某些内容（如上面例子中的tooltip或者view more按钮）

问题在于，css引擎的渲染方式js是无法直接干预的，所以就算是显示出了...省略号，css不可能去通知js。这就导致向上面的这些需求具有一定的难度。

解决上面问题的一个神器就是**ResizeObserver**，刚才说了，css更新样式之后不会通知js是不严谨的。事实上，通过**ResizeObserver**就可以让css更新之后给js发送消息，从而执行回调。

下面就以笔者在项目中的实际开发过程作为样例，叙述**ResizeObserver**在解决这类问题时候的一般解决方案。

## 1. 需求叙述
在移动端增加一个article，要求其中的文字最多为11行，超出11行之后显示view more，点击view more就可以完全展开文章，同时view more变成view less；如果文章本来就小于11行，则按照文章实际大小展示，同时不出现view more按钮。

## 2. 控制变量
使用控制变量**viewMore**控制按钮显示的文字为"view more"还是"view less"。使用控制变量**showMoreOrLess这个布尔值**控制按钮显示的文字为"view more"还是"view less"。
```js
  constructor(props) {
    super(props);
    this.state = {
      viewMore: true,
      showMoreOrLess: true,
    };
  }
```

## 3. view more or less按钮
点击这个按钮可以收起或者展开文章组件
```jsx
{
  this.state.showMoreOrLess ? <span
    className={"view-more-less"}
    onClick={() => { this.setState({ viewMore: !this.state.viewMore }) }}
  >
    {this.state.viewMore ? intl.get('button_view_more') : intl.get('button_view_less')}
  </span> : <></>
}
```

## 4. 使用监听类监听文章组件的样式
```jsx
bindResize = ele => {
this.resizeObserver = new ResizeObserver(() => {
  const { clientHeight, scrollHeight } = ele;
  this.setState({ showMoreOrLess: scrollHeight > clientHeight });
});
this.resizeObserver.observe(ele);
};
```

## 5. 文章组件
文章组件的ref指向bindResize这个方法即可
```jsx
<div
    ref={this.bindResize}
    className={`desc-content ${this.state.viewMore ? "viewMore" : "viewLess"}`}
>
    {articleContent}
</div>
```

## 6. 原理解释
由于文章组件的ref指向了bindResize，因此文章组件在渲染完成之后就被ResizeObserver监听了起来；当文章组件中的文字渲染完毕之后，它的样式一定会发生变化，从而导致回调的执行，此时回调函数中将会对比scrollHeight和clientHeight的大小，如果前者大则证明超出了规定的长度，这个时候调用this.setState修改控制变量的布尔值，就可以实现需求了。

## 7. 垃圾回收
不要忘记对监听器进行回收，这也是为什么要用一个类属性保存监听对象的原因了:
```jsx
componentWillUnmount() {
    if (this.resizeObserver) this.resizeObserver.disconnect();
}
```