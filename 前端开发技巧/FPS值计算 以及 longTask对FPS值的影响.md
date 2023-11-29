# 计算最近 FPS 的值及使用 FPS 指标反映页面流畅性的可靠性检测

在现代的 Web 开发中，页面的流畅性是非常重要的，而 FPS（Frames Per Second，每秒帧数）是衡量页面流畅性的关键指标之一。借助这一指标，可以评估并优化页面的渲染性能，从而提供更出色的用户体验。本文将详细讨论如何计算最近 FPS 的值，以及使用 FPS 指标反映页面流畅性的可靠性检测。

## 什么是 FPS？

FPS 是指每秒钟页面呈现的帧数，通常用于衡量页面的流畅程度。高 FPS 值表示页面以更高的速率进行更新，用户会感受到更加流畅的交互和动画效果。在浏览器中，通常我们希望页面的 FPS 能够达到 60 或者更高，以确保用户的体验。

## 计算最近 FPS 的值

本文中提供的代码示例可以用来计算最近 FPS 的值。

```js
const longTask = (t,gap) => { while(performance.now()-t<gap){} };

function FPS(_gap){

    let count = 0;
    let execTimes = [];
    let gap = _gap ?? 1/1000;

    function callback(){
        const t = performance.now();
        longTask(t,gap);
        execTimes.push(performance.now());
        count++;
        if(count<101){
            window.requestAnimationFrame(callback);
        } else {
            const FPS = calculateFPS(execTimes);
            console.log('最近的FPS的值为： ', FPS);
        }
    }

    function calculateFPS(dataArray){
        const len = dataArray.length;
        return 1000*(len-1)/(dataArray[len-1]-dataArray[0]);
    }

    window.requestAnimationFrame(callback);
}
```

原理：
这段代码中的 `FPS` 函数被调用以计算最近的 FPS 值。

-   首先，我们定义了一些变量，包括计数器变量 `count`、执行时间点数组 `execTimes`、时间间隔 `gap`。
-   然后，我们定义了 `callback` 函数，它被传递给 `window.requestAnimationFrame` 方法，以便在每个动画帧上执行。
-   在 `callback` 函数中，我们记录了每个动画帧的执行时间点，并将其存储在 `execTimes` 数组中。
-   当达到一定数量的动画帧时（在这里是 100 帧），我们调用了 `calculateFPS` 函数来计算最近的 FPS 值。
-   在 `calculateFPS` 函数中，我们使用执行时间点数组中的第一个和最后一个时间戳，并将其与数组的长度相结合，计算出最近的 FPS 值。

使用说明：
上面的代码中，使用`longTask`函数来模拟两个渲染帧之间执行的其他js宏任务；gap的单位是ms，表示这个任务执行用时。

FPS函数是用来计算最近浏览器的FPS的；执行FPS()就可以得到浏览器idle时候的FPS，一般在60左右，这一点符合常识； 而执行FPS(100)则可以得到两个渲染帧之间执行耗时100ms的任务时对应的FPS。

## longTask对FPS的影响
### 1. FPS的执行结果

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bd53df768d83481fb0287badfbd4c6be~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=340&h=91&s=3489&e=png&b=ffffff)
### 2. FPS在不同入参下执行的结果

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2d307f40b6594966af36943a4fab46b1~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=372&h=647&s=30233&e=png&b=ffffff)

## 总结

本文介绍了计算最近 FPS 值的方法以及使用 FPS 指标来反映页面流畅性的可靠性检测。讨论了 FPS 指标的意义和计算原理。通过理解 FPS 指标的概念和应用，开发者能够更好地评估页面的流畅性，并采取相应的优化措施，提供更出色的用户体验。

希望读者能够对计算最近 FPS 的值以及使用 FPS 指标反映页面流畅性的可靠性检测有更深入的理解。在实际开发中，可以使用类似的方法来评估页面的渲染性能，并进行相应的优化。掌握这些技巧，将有助于提供更好的网页体验，并提升用户满意度。