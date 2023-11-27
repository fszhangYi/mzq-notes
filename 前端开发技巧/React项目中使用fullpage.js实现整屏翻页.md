最近笔者在完成一个移动端小项目的过程中需要实现整屏翻页的效果；调研完毕之后，最终决定使用pullPage.js实现此功能。理由也很简单，fullPage.js使用起来比较方便，并且官网上也给了在react项目中使用的demo。本文记录了这个第三方库的使用过程。

## 1. 安装
```bash
yarn add fullpage.js
```

## 2. 项目中引入
```js
import fullpage from 'fullpage.js';
import 'fullpage.js/dist/fullpage.css';
```

## 3. 挂载
在dom加载完成之后，实例化fullpage
```js
  useEffect(() => {
    new fullpage('#fullpage', {
      credits: { enabled: false, label: '', position: 'right' },
      // fullpage.js的配置选项
      // 例如：sectionsColor, navigation等
    });
  }, []);
```

`new fullpage`的时候会自动去寻找**id为fullpage的dom元素**，因此一定保证根元素的id为**fullpage**，如下所示：

```jsx
    <div id="fullpage">
      <div className='section' style={{ boxSizing: 'border-box', height: window.innerHeight }}>
      ...
      </div>
    </div>
```

fullpage作用的对象是**页**，而页使用**section**这个类名来表示，如上面的代码所示。

## 4. 修改
fullpage.js夹杂了一些私货，需要手动去除
```css
.fp-overflow {
  height: 100%;
}

.fp-watermark {
  display: none;
}
```
第一个消除的是垂直方向的居中，一般来说不需要；

第二个则是这个组件的商标，也需要去掉。