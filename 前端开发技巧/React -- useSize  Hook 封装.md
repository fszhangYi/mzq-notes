# React -- `useSize` Hook 封装

在开发React应用时，经常需要响应元素尺寸的变化，以提供更好的用户体验。本文将介绍如何封装一个自定义的`useSize` Hook，它可以监听某个元素的大小变化并返回其宽高。

### 背景知识

在React中，自定义Hook是一种重用状态逻辑的机制。`useSize` Hook将返回所观察元素的宽度和高度。

### 使用useState和useLayoutEffect

引入`useState`和`useLayoutEffect`钩子。`useState`用来存储元素的宽度和高度，而`useLayoutEffect`用来订阅尺寸变化。

### 响应尺寸变化

通过`ResizeObserver` API，可以监听元素尺寸的变化。当被监听元素尺寸发生变化时，`ResizeObserver`将执行一个回调函数，在这个回调函数中，用`setState`更新元素的尺寸信息。

### 清理工作

为了避免内存泄漏，需要在`useLayoutEffect`的清理函数中断开`ResizeObserver`的监听。

### 完整的 Hook 实现

下面是`useSize` Hook 的完整代码实现：

```jsx
import { useState, useLayoutEffect } from 'react';
import ResizeObserver from 'resize-observer-polyfill';

function useSize(target) {
  const [state, setState] = useState(() => {
    return {
      width: target ? target.clientWidth : 0,
      height: target ? target.clientHeight : 0,
    };
  });

  useLayoutEffect(() => {
    if (!target) {
      return () => {};
    }

    const resizeObserver = new ResizeObserver(entries => {
      entries.forEach(entry => {
        setState({
          width: entry.target.clientWidth,
          height: entry.target.clientHeight,
        });
      });
    });

    resizeObserver.observe(target);

    return () => {
      resizeObserver.disconnect();
    };
  }, [typeof target === 'function' ? undefined : target]);

  return state;
}

export default useSize;
```

这个Hook将返回一个包含`width`和`height`属性的对象，且会在被观察元素的尺寸变化时更新这两个属性。结合实际情况，你可以很容易地将这个Hook集成到你的React组件中。

### 使用方式

你可以在组件中调用`useSize`，并传递一个DOM元素引用（`ref`）作为参数。这个Hook将返回该元素的宽度和高度。

```jsx
  const chartRef = useRef(null);
  const size = useSize(chartRef.current);
```

### 小结

`useSize` Hook 提供了一种简洁而又高效的方式来监听和响应React元素的尺寸变化。使用这种模式可以减少冗余代码，并提高组件的可维护性。