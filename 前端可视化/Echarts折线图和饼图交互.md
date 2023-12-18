# Echarts折线图和饼图交互

## 引言
Echarts是一个功能强大的JavaScript图表库，提供了许多可视化选项。本文将介绍如何使用Echarts创建一个折线图和饼图，并实现它们之间的交互效果。

## 折线图和饼图的基本设置
首先，我们需要引入Echarts库，并创建一个容器来放置我们的图表：

```javascript
// 引入Echarts库
import * as echarts from 'echarts';

// 创建容器
<div id="line-chart"></div>
<div id="pie-chart"></div>
```

接下来，我们分别初始化折线图和饼图的实例，并设置它们的基本配置：

```javascript
// 初始化折线图
const lineChart = echarts.init(document.getElementById('line-chart'));
lineChart.setOption({
    ...,
    // 设置折线图的数据和样式
    series: [{
        type: 'line',
        data: [10, 20, 30, 40, 50],
        lineStyle: {
            color: 'blue',
            width: 2
        },
        itemStyle: {
            color: 'blue'
        }
    }],
    ...
});

// 初始化饼图
const pieChart = echarts.init(document.getElementById('pie-chart'));
pieChart.setOption({
    ...,
    // 设置饼图的数据和样式
    series: [{
        type: 'pie',
        data: [
            { value: 10, name: 'A' },
            { value: 20, name: 'B' },
            { value: 30, name: 'C' },
            { value: 40, name: 'D' },
            { value: 50, name: 'E' }
        ],
        label: {
            formatter: '{b}: {d}%'
        },
        ...
    }],
    ...
});
```

## 实现折线图和饼图的交互效果
现在，我们将实现折线图和饼图之间的交互效果。当用户点击折线图上的数据点时，饼图应该显示对应数据点的细节。

首先，我们需要给折线图添加一个点击事件处理程序，以获取用户点击的数据点：

```javascript
lineChart.on('click', params => {
    const data = params.data; // 获取点击的数据

    // 更新饼图的数据和样式
    pieChart.setOption({
        ...,
        series: [{
            type: 'pie',
            data: [
                { value: data * 2, name: 'A' },
                { value: data * 3, name: 'B' },
                { value: data * 4, name: 'C' },
                { value: data * 5, name: 'D' },
                { value: data * 6, name: 'E' }
            ]
        }],
        ...
    });
});
```

上述代码中，我们通过params对象获取用户点击的数据点的值，并根据这个值更新饼图的数据。在这个例子中，我们将每个数据点的值乘以不同的系数来生成饼图的数据。

接下来，我们需要添加一些交互的效果，例如在用户悬停在折线图上的数据点时，饼图应该高亮显示相应的数据。为此，我们可以使用Echarts中的tooltip组件来实现。

```javascript
// 设置折线图的tooltip样式和内容
lineChart.setOption({
    ...,
    tooltip: {
        trigger: 'axis',
        formatter: params => {
            const value = params[0].data;
            return `Value: ${value}`;
        }
    },
    ...
});

// 设置饼图的tooltip样式和内容
pieChart.setOption({
    ...,
    tooltip: {
        trigger: 'item',
        formatter: '{b}: {d}%'
    },
    ...
});
```

通过设置tooltip的trigger属性为'axis'，我们可以让折线图的tooltip在鼠标悬停在数据点上时显示。在formatter函数中，我们可以自定义tooltip的内容。同样，我们也为饼图设置了tooltip的样式和内容。

最后，我们需要在页面加载时初始化这两个图表：

```javascript
window.onload = () => {
    lineChart.resize();
    pieChart.resize();
};
```

以上代码确保页面加载时正确显示图表，并且在窗口大小改变时重新调整图表的大小。

## 结论
本文介绍了如何使用Echarts创建一个折线图和饼图，并实现它们之间的交互效果。通过点击折线图上的数据点，我们可以在饼图中显示相应的数据细节。另外，我们还为折线图和饼图设置了tooltip，以提供更多的交互效果。希望本文对您在使用Echarts创建交互式图表时有所帮助。

参考资料：
- Echarts官方文档: [https://echarts.apache.org/zh/index.html](https://echarts.apache.org/zh/index.html)