# ECharts堆叠柱状图

数据可视化是现代数据分析中不可或缺的一部分，它将复杂的数据集以图形的方式直观展现。ECharts是一款由百度团队开源的数据可视化库，它提供了丰富的图表类型和灵活的配置选项，非常适合快速构建多种交互式图表。本文将重点介绍ECharts中的堆叠柱状图的制作过程，包括基本配置、数据设置和样式调整。

## 什么是堆叠柱状图

堆叠柱状图是一种将多个数据系列以条块的形式按顺序堆叠在一起的柱状图，它用于显示各个系列数据在不同分类中的分布情况及总量。通过堆叠柱状图，观者可以方便地比较不同系列在总体中的占比，也可以快速获取每个分类的综合值。

## ECharts堆叠柱状图的基本配置

在ECharts中，要配置一张堆叠柱状图，首先要定义`option`对象。`option`对象中可包含配置项，诸如`tooltip`、`legend`、`xAxis`、`yAxis`和`series`等。

```javascript
option = {
  tooltip: {
    trigger: 'axis',
    axisPointer: {
      type: 'shadow'
    }
  },
  legend: {},
  grid: {
    left: '3%',
    right: '4%',
    bottom: '3%',
    containLabel: true
  },
  xAxis: [
    {
      type: 'category',
      data: [] // X轴的数据，在这里设置具体的分类名
    }
  ],
  yAxis: [
    {
      type: 'value' // Y轴的数据类型，这里是数值型
    }
  ],
  series: [] // 系列列表。每个系列通过 type 决定自己的图表类型
};
```

### Tooltip的配置

`tooltip`是图表的提示框配置项，它在用户鼠标悬浮在图表项上时显示详细的数据信息。在堆叠柱状图中，通常配置`axis`类型的提示框，因为它可以一次性展示一个类别上所有系列数据的信息。

### Legend的配置

`legend`控制图例组件的显示。图例用于解释图表中各个不同数据系列的图示，用户可以通过点击图例控制哪些系列被显示或隐藏。

### Grid的配置

`grid`控制图表的网格位置，在这里可以调整图表的边距，使图表在容器中更好地展示。

### xAxis和yAxis的配置

`xAxis`和`yAxis`配置项用以定义坐标轴。在类别型的堆叠某状图中，通常将`xAxis`设为`category`类型，并在`data`属性中配置分类数据。`yAxis`则常设置为`value`数值轴。

### Series的配置

`series`是定义数据系列的配置项，在堆叠柱状图中每个数据系列由一个对象表示，每个对象需包含`name`、`type`、`data`等属性。关键的`stack`属性将系列设置为堆叠，其值为相同的堆叠名称表示这些系列将堆叠在一起。

## ECharts堆叠柱状图的数据设置

在ECharts的堆叠柱状图中，`series`的`data`属性用于设置每个系列的数据值。以给定的配置为例，定义七天每天的数据，其中包括Direct、Email、Union Ads等不同来源的数据，并且有的系列会进行堆叠。

```javascript
series: [
  {
    name: 'Direct',
    type: 'bar',
    data: [320, 332, 301, 334, 390, 330, 320]
    // 其他配置...
  },
  {
    name: 'Email',
    type: 'bar',
    stack: 'Ad',
    data: [120, 132, 101, 134, 90, 230, 210]
    // 其他配置...
  },
  // ...其他系列数据
]
```

## ECharts堆叠柱状图的样式调整

ECharts为开发者提供了强大的样式配置选项。可以对图表的每个部分进行个性化设置，比如修改柱状图的颜色、设置柱体的宽度、添加标记线等。

### 修改颜色和宽度

通过在`series`的每个对象中设置`itemStyle`属性，可以设定每个系列的颜色；`barWidth`属性则用于控制柱状图的宽度。

### 添加标记线

ECharts允许用户在系列中添加特定的`markLine`，如最大值线、最小值线等。通过在`series`对象中配置相应的`markLine`即可。

## 完整代码示例

结合上述各部分配置，下面给出了ECharts堆叠柱状图的完整代码示例：

```javascript
option = {
  tooltip: {
    trigger: 'axis',
    axisPointer: {
      type: 'shadow'
    }
  },
  legend: {},
  grid: {
    left: '3%',
    right: '4%',
    bottom: '3%',
    containLabel: true
  },
  xAxis: [
    {
      type: 'category',
      data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
    }
  ],
  yAxis: [
    {
      type: 'value'
    }
  ],
  series: [
    {
      name: 'Direct',
      type: 'bar',
      emphasis: {
        focus: 'series'
      },
      data: [320, 332, 301, 334, 390, 330, 320]
    },
    // ...其他系列配置
    {
      name: 'Search Engine',
      type: 'bar',
      data: [862, 1018, 964, 1026, 1679, 1600, 1570],
      emphasis: {
        focus: 'series'
      },
      markLine: {
        lineStyle: {
          type: 'dashed'
        },
        data: [[{ type: 'min' }, { type: 'max' }]]
      }
    }
    // ...其他系列配置
  ]
};
```

请将此JavaScript代码段番茸你项目的相应文件中，并确保该文件已正确引入ECharts库。然后可以在页面上初始化一个ECharts实例，并将此配置项`option`赋给它：

```javascript
var myChart = echarts.init(document.getElementById('main'));
myChart.setOption(option);
```

## 结语

ECharts的堆叠柱状图是展示多系列数据的理想工具，通过适当的配置可以制作出既美观又功能丰富的图表。通过本文提供的指南和示例代码，相信即使是ECharts的初学者也能快速入门，创建出符合需求的数据可视化图表。