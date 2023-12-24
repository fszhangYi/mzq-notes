# Echarts正负条形图

正负条形图是一种常见的数据可视化图表，用于展示某个变量在不同分类下的正负值分布情况。在众多图表类型中，正负条形图以其直观的形态和明确的对比效果，在财务分析、项目评价、利益比较等领域得到广泛应用。Echarts正负条形图不仅能够简洁地表达正负数据，还能通过强大的交互功能增强数据呈现的效果。

本篇文章将详细讲解Echarts正负条形图的基本概念、配置逻辑以及一个具体的例子，并提供相应的实现代码，以帮助开发者更好地利用这一工具展示数据。

## 正负条形图的概念

正负条形图通常将条形的正负两个方向用于表示不同性质的数据，例如盈利（利润）与亏损，收入与支出等。图表的横轴（x轴）一般用于表示数值大小，而纵轴（y轴）则展现分类信息。条形的长度代表数值大小，方向则表明正负属性。

## Echarts正负条形图配置

一个基础的Echarts正负条形图配置包含了工具提示（tooltip）、图例（legend）、网格（grid）、横轴（xAxis）、纵轴（yAxis）以及系列（series）等关键组件。这些组件协同工作，构成了图表的数据呈现和交互体系。

以下是一个基础的Echarts正负条形图配置代码示例：

```js
option = {
  tooltip: {
    trigger: 'axis',
    axisPointer: {
      type: 'shadow'
    }
  },
  legend: {
    data: ['Profit', 'Expenses', 'Income']
  },
  grid: {
    left: '3%',
    right: '4%',
    bottom: '3%',
    containLabel: true
  },
  xAxis: [
    {
      type: 'value'
    }
  ],
  yAxis: [
    {
      type: 'category',
      axisTick: {
        show: false
      },
      data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
    }
  ],
  series: [
    {
      name: 'Profit',
      type: 'bar',
      label: {
        show: true,
        position: 'inside'
      },
      emphasis: {
        focus: 'series'
      },
      data: [200, 170, 240, 244, 200, 220, 210]
    },
    {
      name: 'Income',
      type: 'bar',
      stack: 'Total',
      label: {
        show: true
      },
      emphasis: {
        focus: 'series'
      },
      data: [320, 302, 341, 374, 390, 450, 420]
    },
    {
      name: 'Expenses',
      type: 'bar',
      stack: 'Total',
      label: {
        show: true,
        position: 'left'
      },
      emphasis: {
        focus: 'series'
      },
      data: [-120, -132, -101, -134, -190, -230, -210]
    }
  ]
};
```

## 配置解读

在这个配置中，`tooltip`属性定义了鼠标悬停在条形上时的提示行为，`axisPointer`的`shadow`类型提供了一个指示器阴影来标明当前条形。`legend`组件显示了包含在图表中的所有系列的名称，允许用户通过点击来筛选想要查看的数据系列。

`grid`组件确保了图表的位置和内部标签的留白。`xAxis`和`yAxis`则分别配置了横轴和纵轴的类型和数据。在此示例中，横轴被设置为数值轴，而纵轴则为分类轴，标签不显示刻度线。

`series`数组中包含了三个对象，每个对象代表了图表中的一个数据系列：

1. **Profit（利润）**系列为标准条形图表示，用于展示正的利润值。
2. **Income（收入）**和**Expenses（支出）**系列利用堆叠的方式展现，这意味着其数据会基于前一系列的数据来进行显示。收入系列以正值条形图呈现，而支出系列通过负值条形图呈现，达到正负对比效果。

每个系列中的`label`属性设置了标签的显示与位置，加强了图表的可读性。`emphasis`对象内的`focus`属性为`'series'`时，则当鼠标悬停在某条数据上时，突出显示该系列。