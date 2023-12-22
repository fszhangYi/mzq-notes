# Echarts点击添加折线图拐点

Apache ECharts（简称ECharts）可谓是数据可视化领域的一款神器。它不仅拥有强大的图表展示能力，同时提供了丰富的用户交互接口。在众多特性中，图表的拐点操作尤为吸引眼球，它增添了用户交互的趣味性，并帮助用户更直观地分析和理解数据。

本文将解读如何在ECharts中实现一个具有交互功能的折线图，使用户可以自主添加新的数据点。

## 实现原理

实现点击添加折线图拐点的功能，主要涉及以下几个步骤：

1. 创建基础的ECharts折线图配置。
2. 监听画布（canvas）的点击事件。
3. 将点击位置的像素坐标转换成图表的数据坐标。
4. 更新数据集，添加新的数据点。
5. 刷新图表，使新的数据点展示在折线图上。

接下来，让我们通过具体的代码和注释，一步步揭开如何在ECharts中添加拐点的过程。

## 基础折线图配置

首先，需要设置基本的折线图。这里定义了一个有初始数据点的简单折线图。每个数据点由其`x`和`y`坐标组成。

```javascript
const symbolSize = 20; // 设置拐点的大小
const data = [ // 初始的数据点数组
  [15, 0],
  [-50, 10],
  [-56.5, 20],
  [-46.5, 30],
  [-22.1, 40]
];
option = {
  title: { // 标题配置
    text: 'Click to Add Points'
  },
  tooltip: { // 提示框配置
    formatter: function (params) {
      var data = params.data || [0, 0];
      return data[0].toFixed(2) + ', ' + data[1].toFixed(2);
    }
  },
  grid: { // 直角坐标系内绘图网格配置
    left: '3%',
    right: '4%',
    bottom: '3%',
    containLabel: true
  },
  xAxis: { // X轴配置
    min: -60,
    max: 20,
    type: 'value',
    axisLine: { onZero: false }
  },
  yAxis: { // Y轴配置
    min: 0,
    max: 40,
    type: 'value',
    axisLine: { onZero: false }
  },
  series: [{ // 系列列表配置
    id: 'a',
    type: 'line',
    smooth: true, // 开启平滑曲线
    symbolSize: symbolSize, // 拐点大小设置
    data: data // 数据源
  }]
};
```

折线图的基本设置完成后，下一步将是监听画布上的事件，并对事件做出相应的处理。

## 监听画布点击事件

为了处理用户的点击事件，在画布上添加`click`事件监听。

```javascript
var zr = myChart.getZr(); // 获取ECharts实例的渲染器

zr.on('click', function (params) { // 监听画布的点击事件
  var pointInPixel = [params.offsetX, params.offsetY]; // 获取点击位置的像素坐标
  var pointInGrid = myChart.convertFromPixel('grid', pointInPixel); // 像素坐标转换为图表数据坐标

  // 判断点击位置是否在直角坐标系(grid)内
  if (myChart.containPixel('grid', pointInPixel)) {
    data.push(pointInGrid); // 将新拐点的数据添加到数据集中
    myChart.setOption({ // 刷新ECharts实例，显示新的拐点
      series: [
        {
          id: 'a',
          data: data
        }
      ]
    });
  }
});
```

在上面的代码中，首先获取ECharts实例的渲染器对象。当用户点击画布时，起点的像素坐标被定位，然后这些像素坐标被转换为ECharts实例的数据坐标，以确保新数据点的精确定位。如果点击事件发生在坐标系(grid)内，那么新的数据点将会被添加到数据集，并且实时更新图表。

## 鼠标移动时改变光标样式

此外，为了提高用户体验，需要给用户适当的反馈。在本例中，当鼠标移动到坐标系上时，会改变鼠标的样式。

```javascript
zr.on('mousemove', function (params) { // 监听鼠标移动事件
  var pointInPixel = [params.offsetX, params.offsetY]; // 获取鼠标位置的像素坐标
  // 判断鼠标位置是否在直角坐标系(grid)内
  zr.setCursorStyle( 
    myChart.containPixel('grid', pointInPixel) ? 'copy' : 'default'
  );
});
```

当鼠标在坐标系内移动时，光标样式会改变为`copy`，提示用户可以在此处添加一个新的数据点。当鼠标移出坐标系时，光标样式会恢复到默认。

## 结语

通过上述步骤，就可以在ECharts中实现添加折线图拐点的交互体验。这种互动性不仅能够增强图表的功能性，并且为用户分析数据提供了便利。

此外，ECharts的事件和方法提供了强大且灵活的定制能力，使得在图表上实现各种高级交互成为可能。无论是在分析趋势、识别模式还是简单地与数据"对话"，ECharts都会是一个十分有力的工具。

文章中的案例代码旨在提供一个如何开始的指南，并且鼓励读者根据不同需求做进一步探索和创新。ECharts的官方文档和示例库也是探索新特性、学习更多交互技巧的宝库。在图表设计和用户交互的旅程中，ECharts为数据的可视化之路添砖加瓦。