# ECharts堆叠柱状图的归一化和变化

数据可视化是将数据转换为视觉上更易于理解的形式的一种技术。在许多情景下，特别是那些涉及到时间序列或多个数据系列的场合，理解数据如何随时间变化成为重要需求。ECharts是一个开源的数据可视化库，它支持多种丰富的图表类型和动态交互能力，其中堆叠柱状图因其突出的数据对比效果被广泛应用。然而，为了更好地展现数据随时间变化的相对关系，有时需要将堆叠柱状图转换为归一化堆叠柱状图并展现其变化趋势。本文将详述如何利用ECharts实现堆叠柱状图的归一化处理，并通过特定的可视化技术显示数据的变化。

## 堆叠柱状图归一化的目的

归一化处理是统计学中的常见技巧，将不同量级或单位的数据转换到同一标准，这样便于从相对层面对数据进行比较。在堆叠柱状图中，尤其是数据量级差别较大时，如果直接以原始数值进行展示，很难观察到各部分数据的相对重要性。通过归一化，将数据转换为百分比形式，易于比较和理解各部分在整体中所占的比重。

## 归一化堆叠柱状图的数据计算

归一化的第一步是计算出堆叠柱状图中每个类别的数据总和，下面的JavaScript代码段实现了这一计算：

```javascript
const rawData = [
  // ...原始数据...
];

const totalData = [];
for (let i = 0; i < rawData[0].length; ++i) {
  let sum = 0;
  for (let j = 0; j < rawData.length; ++j) {
    sum += rawData[j][i];
  }
  totalData.push(sum);
}
```

`totalData`数组中的每个元素代表每个类别（比如星期一至星期日）的所有系列数据的总和。

## 配置ECharts系列数据

接下来是创建归一化的ECharts系列数据，为了使数据展示为百分比形式，使用`label`属性在柱状图上显示数据标签，并配置其`formatter`格式化函数：

```javascript
const series = [
  'Direct',
  'Mail Ad',
  // ...
].map((name, sid) => {
  return {
    name,
    type: 'bar',
    stack: 'total',
    barWidth: '60%',
    label: {
      show: true,
      formatter: (params) => Math.round(params.value * 1000) / 10 + '%'
    },
    data: rawData[sid].map((d, did) =>
      totalData[did] <= 0 ? 0 : d / totalData[did]
    )
  };
});
```

这段代码计算了每个系列在每个类别中所# ECharts堆叠柱状图的归一化和变化

在复杂的数据展示场景下，堆叠柱状图是表现多维度数据相对关系的有效工具。ECharts，作为一个强大的数据可视化库，它支持丰富的图表类型以及灵活的配置，让数据的展示更加生动和直观。堆叠柱状图的归一化处理便于比较不同分类间各系列的相对比例；同时，通过在堆叠柱状图之间添加变化形态，可以直观展现数据随时间或其他类别变化的趋势。本文将详细介绍如何在ECharts中实现堆叠柱状图的归一化和变化展示，并通过具体代码实例进行解释。

## 归一化的重要性与计算

在不同数量级的数据对比中，单纯的堆叠柱状图可能无法直观地反映数据的相对重要性，归一化处理可以将不同类别的数据按照各自总量的比例展示，实现了不同数据间的有效比较。以下将通过ECharts配置及JavaScript计算逻辑来完成归一化的处理。

首先，需要计算各分类（例如周一至周日）在所有系列（如直接访问、邮件广告等）中的数据总量。

```javascript
const rawData = [
  [100, 302, 301, 334, 390, 330, 320],
  // ...其他原始数据组
];

const totalData = [];
for (let i = 0; i < rawData[0].length; ++i) {
  let sum = 0;
  for (let j = 0; j < rawData.length; ++j) {
    sum += rawData[j][i];
  }
  totalData.push(sum);
}
```

## 构建归一化的堆叠柱状图系列

在ECharts中，`series`数组中的每个对象代表一个数据系列。为了进行归一化，需要将每个系列的原始数据除以对应分类的总数据量，转化为比例形式。

```javascript
const series = [
  'Direct',
  'Mail Ad',
  // ...其他系列名称
].map((name, sid) => {
  return {
    name,
    type: 'bar',
    stack: 'total',
    barWidth: '60%',
    label: {
      show: true,
      formatter: (params) => Math.round(params.value * 1000) / 10 + '%'
    },
    data: rawData[sid].map((d, did) => totalData[did] <= 0 ? 0 : d / totalData[did])
  };
});
```

在上述代码中，每一系列的数据通过归一化后的计算得到，每个柱子上显示了该系列占总和的百分比。

## 实现堆叠柱状图的变化形态

数据的动态变化可以使用ECharts的`graphic`组件来直观展示。这部分的实现思路是在相邻的两个堆叠柱状图之间绘制多边形，以表示从一个状态到另一个状态的过渡。

```javascript
const grid = {
  // ...网格配置
};
const gridWidth = myChart.getWidth() - grid.left - grid.right;
const gridHeight = myChart.getHeight() - grid.top - grid.bottom;
const categoryWidth = gridWidth / rawData[0].length;
const barWidth = categoryWidth * 0.6;
const barPadding = (categoryWidth - barWidth) / 2;
const color = ['#5470c6', '#91cc75', '#fac858', '#ee6666', '#73c0de'];
const elements = [];

for (let j = 1, jlen = rawData[0].length; j < jlen; ++j) {
  // ...计算多边形顶点坐标
  for (let i = 0, len = series.length; i < len; ++i) {
    // ...数据变化路径的多边形处理
  }
}
```

以上代码中的循环逻辑是用于构建多边形形态，示意从时间点`j-1`到`j`堆叠柱状图数据的变化。通过每个系列相应的颜色和透明度，能够形象地表现数据的变化趋势。

## ECharts配置的集成

最终，所有配置项集成到ECharts的`option`中，包括网格、类别轴、数值轴、归一化的系列数据以及多边形的图形元素。

```javascript
option = {
  // ...图例、网格、坐标轴配置
  series: series,
  graphic: {
    elements: elements
  }
};
```

`series`配置项提供了归一化后的堆叠柱状图，而`graphic`配置中的`elements`提供了数据变化的多边形形态。

## 图表实例化与渲染

最后将准备好的配置项赋值给ECharts实例，并指定到页面中DOM元素，以展示归一化和变化的堆叠柱状图。

```javascript
var myChart = echarts.init(document.getElementById('main'));
myChart.setOption(option);
```

在具有`id="main"`的DOM元素中呈现图表，用户会看到归一化的数据比例和随类别变化的动态效果。

## 总结

通过归一化处理，堆叠柱状图的比较更加公平，特别是对于数量级相差悬殊的数据。在此基础上，添加的变化形态使得数据的动态趋势变得形象且易于解读。ECharts提供的配置和图形绘制功能，使得以上需求的实现变得简单而高效。通过本文的解析和代码示例，数据可视化的实现者能够更好地掌握如何利用ECharts实现更加丰富和有吸引力的数据展示。