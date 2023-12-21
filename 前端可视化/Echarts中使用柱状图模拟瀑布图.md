# Echarts中使用柱状图模拟瀑布图

## 引言

瀑布图（Waterfall Chart）是数据可视化中的一种特殊形式的柱状图，通常用于展示量的累计效应。通过对起始值和结束值之间的中间过程进行分步展示，瀑布图能够清晰地展现某项指标的增减变化。由于Echarts原生并未提供专门的瀑布图类型，因此可以使用柱状图通过特殊的方式来模拟瀑布图效果。本文旨在介绍如何利用Echarts的柱状图组件，配合相应的配置，实现类似瀑布图的数据展示效果，以及提供具体的示范代码。

## 瀑布图简介

瀑布图通常用来表现一个量在一段时间内或者在各个组成部分之间的递增或递减过程。在财务报告中，它可以展示总收入在经过一系列积极或消极的变化后成为最终利润的过程。

传统的瀑布图包含三类柱体：

1. 初始值和最终值的柱子，通常使用特殊颜色或图例标识。
2. 增量柱体，表示增加的量，可以着色为正数的颜色，如绿色。
3. 减量柱体，表示减少的量，可以着色为负数的颜色，如红色。

对于Echarts中尚未内置的瀑布图，可通过叠加柱状图来模拟。下面将介绍如何利用Echarts配置出类似效果。

## 配置项解析

### title

图表的标题组件，至关重要，能明确传达图表的主要内容。

```javascript
title: {
  text: 'Waterfall Chart', // 主标题文字
  subtext: 'Living Expenses in Shenzhen' // 副标题文字
},
```

### tooltip

提示框组件，用于指导用户了解各柱体的详细信息。

```javascript
tooltip: {
  trigger: 'axis', // 触发类型：坐标轴触发
  axisPointer: {
    type: 'shadow' // 指示器类型：阴影指示器
  },
  formatter: function (params) { // 自定义提示框的显示内容
    var tar = params[1];
    return tar.name + '<br/>' + tar.seriesName + ' : ' + tar.value;
  }
},
```

### grid

直角坐标系内绘图网格，可以调整图表的边距等属性。

```javascript
grid: {
  left: '3%',
  right: '4%',
  bottom: '3%',
  containLabel: true // 包含刻度标签在内
},
```

### xAxis

类目型直角坐标系中的横轴。

```javascript
xAxis: {
  type: 'category', // 坐标轴类型：类目轴
  splitLine: { show: false }, // 不显示分隔线
  data: ['Total', 'Rent', 'Utilities', 'Transportation', 'Meals', 'Other'] // 类目数据
},
```

### yAxis

直角坐标系中的纵轴。

```javascript
yAxis: {
  type: 'value' // 坐标轴类型：数值轴
},
```

### series

系列列表。在瀑布图的模拟中，需要使用两个系列（柱状图），一个作为占位符，用来形成瀑布效果的“流水”，一个作为显示实际值的柱状图。

```javascript
series: [
  {
    name: 'Placeholder', // 占位符系列的名称
    type: 'bar', // 图表类型：柱状图
    stack: 'Total', // 堆叠的名称
    itemStyle: { // 柱状图样式
      borderColor: 'transparent',
      color: 'transparent'
    },
    emphasis: { // 高亮样式
      itemStyle: {
        borderColor: 'transparent',
        color: 'transparent'
      }
    },
    data: [0, 1700, 1400, 1200, 300, 0] // 占位柱状图的数据
  },
  {
    name: 'Life Cost', // 生活成本系列的名称
    type: 'bar', // 图表类型：柱状图
    stack: 'Total', // 堆叠的名称
    label: { // 标签设置
      show: true,
      position: 'inside' // 标签位置：在柱体内部
    },
    data: [2900, 1200, 300, 200, 900, 300] // 实际数据
  }
]
```

在上面的`series`数组中，首个系列配置为透明的占位柱体，用于构造瀑布图的开始点和中间步骤的结束点。第二个系列则用实际数据显示每一个生活开销的金额，标签显示在每个柱子的内部，容易直观看出具体数值。

## 代码实例

根据上述配置项，下面是使用Echarts实现瀑布图效果的完整代码：

```javascript
let option = {
  title: {
    text: 'Waterfall Chart',
    subtext: 'Living Expenses in Shenzhen'
  },
  tooltip: {
    trigger: 'axis',
    axisPointer: {
      type: 'shadow'
    },
    formatter: function (params) {
      var tar = params[1];
      return tar.name + '<br/>' + tar.seriesName + ' : ' + tar.value;
    }
  },
  grid: {
    left: '3%',
    right: '4%',
    bottom: '3%',
    containLabel: true
  },
  xAxis: {
    type: 'category',
    splitLine: { show: false },
    data: ['Total', 'Rent', 'Utilities', 'Transportation', 'Meals', 'Other']
  },
  yAxis: {
    type: 'value'
  },
  series: [
    {
      name: 'Placeholder',
      type: 'bar',
      stack: 'Total',
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
      data: [0, 1700, 1400, 1200, 300, 0]
    },
    {
      name: 'Life Cost',
      type: 'bar',
      stack: 'Total',
      label: {
        show: true,
        position: 'inside'
      },
      data: [2900, 1200, 300, 200, 900, 300]
    }
  ]
};
```

## 结语

虽然Echarts本身没有专门的瀑布图类型，但透过巧妙的堆叠柱状图配置，仍然可以实现瀑布图的效果。在柱体叠加的时候，占位柱体的作用是支撑起下一实际值柱体的基座，从而形成连续的累计效果。

瀑布图在展示利润构成、成本分析、项目预算等多方面皆有广泛应用。借助Echarts丰富的配置项和自由度高的可定制性，开发者可以为最终用户提供强大、直观的数据分析图表，以便更好地进行决策支持和策略制定。