# Echarts阶梯瀑布图

Echarts是一款功能强大的数据可视化库，广泛应用于前端开发领域。阶梯瀑布图是一种特殊的条形图，通常用于表示某个数据序列在时间序列中的累积变化。这种图表不仅能清晰反映各时间段的即时增减情况，还可以展现最终结果的构成，是财务分析和数据报告中常见的图表类型之一。接下来，将详细介绍如何使用Echarts创建阶梯瀑布图，并提供一个实际的例子说明其配置及代码实现。

## 初始配置

Echarts阶梯瀑布图的基本构成需要x轴（横轴）来表示时间序列，y轴（纵轴）来表示数值。此外，还需要至少两个系列（series）来分别表示增加和减少的数据。

下面是一个Echarts阶梯瀑布图的基本配置范例：

```js
option = {
  title: {
    text: 'Accumulated Waterfall Chart'
  },
  tooltip: {
    trigger: 'axis',
    axisPointer: {
      type: 'shadow'
    },
    formatter: function (params) {
      let tar;
      if (params[1] && params[1].value !== '-') {
        tar = params[1];
      } else {
        tar = params[2];
      }
      return tar && tar.name + '<br/>' + tar.seriesName + ' : ' + tar.value;
    }
  },
  legend: {
    data: ['Expenses', 'Income']
  },
  grid: {
    left: '3%',
    right: '4%',
    bottom: '3%',
    containLabel: true
  },
  xAxis: {
    type: 'category',
    data: (function () {
      let list = [];
      for (let i = 1; i <= 11; i++) {
        list.push('Nov ' + i);
      }
      return list;
    })()
  },
  yAxis: {
    type: 'value'
  },
  series: [
    {
      name: 'Placeholder',
      type: 'bar',
      stack: 'Total',
      silent: true,
      itemStyle: {
        borderColor: 'transparent',
        color: 'transparent'
      },
      emphasis: {
        itemStyle: {
          borderColor: 'transparent',
          color: 'transparent'
        }
      },
      data: [0, 900, 1245, 1530, 1376, 1376, 1511, 1689, 1856, 1495, 1292]
    },
    {
      name: 'Income',
      type: 'bar',
      stack: 'Total',
      label: {
        show: true,
        position: 'top'
      },
      data: [900, 345, 393, '-', '-', 135, 178, 286, '-', '-', '-']
    },
    {
      name: 'Expenses',
      type: 'bar',
      stack: 'Total',
      label: {
        show: true,
        position: 'bottom'
      },
      data: ['-', '-', '-', 108, 154, '-', '-', '-', 119, 361, 203]
    }
  ]
};
```

## 图表解读

在这个配置中，`series`数组包含了三类数据：

1. **Placeholder**：这是一个辅助系列，用于建立阶梯效果的基础。它的数据对应着每个时间点之前所有数据的累积和。这个系列的样式被设定为透明，以免在图表中显示出来。

2. **Income**：表示收入的系列。该系列的数据显示为正值时，条形会从对应的Placeholder基础上向上延伸。

3. **Expenses**：表示支出的系列。该系列的数据显示为负值时，条形会从对应的Placeholder基础上向下延伸。

## 实例讲解

以一个具体的数据为例，假设有一个企业在11月的前11日有如下的收支记录：

- 收入（Income）：在11月1日收入900，2日收入345，3日收入393，7日收入135，8日收入178，9日收入286。
- 支出（Expenses）：在11月4日支出108，5日支出154，10日支出119，11日支出361和203。

为了在Echarts中为这些数据制作一个阶梯瀑布图，首先需要一个“Placeholder”系列，这个系列在每一个节点上累加前一个节点的值，这样就能构成一个阶梯的基础。以11月5日为例，这一天之前的累计总额是初始值0加上11月1日的900，再加上11月2日的345，再加上11月3日的393，减去11月4日的108支出，因此11月5日的Placeholder值应为1530。

进一步地，11月5日的支出154实际应该从1530起算，向下减去154，意味着为1376。而11月6日由于没有任何收入和支出，Placeholder值则延续11月5日的1376。

通过这种方法，可以为每一天得到一个Placeholder值，然后根据实际的每日收入和支出情况，使用“Income”和“Expenses”系列来填充正负条形，使其基于Placeholder系列显示出来。

## Echarts集成

使用Echarts集成这个阶梯瀑布图涉及到前端 web 技术栈，主要是HTML和JavaScript。开发者需要在HTML中为图表建立一个容器：

```html
<div id="main" style="width: 600px;height:400px;"></div>
```

然后在JavaScript中引入Echarts库并使用上面提供的配置 `option` 初始化图表：

```js
// 基于准备好的dom，初始化echarts实例
const myChart = echarts.init(document.getElementById('main'));
// 使用刚指定的配置项和数据显示图表。
myChart.setOption(option);
```

确保在HTML文件中正确引入了Echarts库的脚本文件。理想情况下，阶梯瀑布图就能成功渲染，并为用户呈现数据变化的直观图形。

## 结语

阶梯瀑布图是一种展示数据变化和累积结果的非常直观和有效的图表类型，Echarts通过其灵活的配置和强大的功能，使得创建阶梯瀑布图变得既简单又有趣。展现数据的同时，它将信息视觉化，为决策提供支持。无论是在财务报告、市场分析还是项目管理领域，它都是一个有价值的工具。