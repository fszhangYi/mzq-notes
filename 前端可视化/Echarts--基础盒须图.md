# Echarts--基础盒须图

数据可视化作为现代信息交互的重要部分，不断被各种行业所应用。图表工具库的选择对于开发者而言极为关键，而Echarts凭借其灵活性和丰富的图表类型，成为了众多开发者的首选。基础盒须图作为一种展示数据分布和离散情况的标准统计图表，经常被用于数据分析中。本文将详细介绍Echarts中如何绘制基础盒须图，并提供详细的代码示例。

## 盒须图简介

盒须图（Boxplot），又称箱线图、箱形图，是一种用作显示一组数据分散情况资料的统计图。它能显示出一组数据的最大值、最小值、中位数、下四分位数（Q1）、上四分位数（Q3）及异常值，非常适合用于多个数据集之间的比较。

盒须图的五个关键数据点为：

- 最小值：数据集中的最小数值。
- 下四分位数（Q1）：位于数据集第一半部分的中位数。
- 中位数：数据集中的中位数。
- 上四分位数（Q3）：位于数据集第二半部分的中位数。
- 最大值：数据集中的最大数值。

此外，盒须图通常还会显示“异常值”，这些是远离四分位数的数据点。

## Echarts 中绘制盒须图

Echarts绘制盒须图需要利用`boxplot`类型的图表，并且通常会和`scatter`类型的图表组合，用以显示数据集中的异常值。以下提供了Echarts 绘制基本盒须图的示例代码：

```javascript
option = {
  title: [
    {
      text: 'Michelson-Morley Experiment',
      left: 'center'
    },
    {
      text: 'upper: Q3 + 1.5 * IQR \nlower: Q1 - 1.5 * IQR',
      borderColor: '#999',
      borderWidth: 1,
      textStyle: {
        fontWeight: 'normal',
        fontSize: 14,
        lineHeight: 20
      },
      left: '10%',
      top: '90%'
    }
  ],
  dataset: [
    {
      source: [
        [850, 740, 900, 1070, 930, 850, 950, 980, 980, 880, 1000, 980, 930, 650, 760, 810, 1000, 1000, 960, 960],
        [960, 940, 960, 940, 880, 800, 850, 880, 900, 840, 830, 790, 810, 880, 880, 830, 800, 790, 760, 800],
        [880, 880, 880, 860, 720, 720, 620, 860, 970, 950, 880, 910, 850, 870, 840, 840, 850, 840, 840, 840],
        [890, 810, 810, 820, 800, 770, 760, 740, 750, 760, 910, 920, 890, 860, 880, 720, 840, 850, 850, 780],
        [890, 840, 780, 810, 760, 810, 790, 810, 820, 850, 870, 870, 810, 740, 810, 940, 950, 800, 810, 870]
      ]
    },
    {
      transform: {
        type: 'boxplot',
        config: { itemNameFormatter: 'expr {value}' }
      }
    },
    {
      fromDatasetIndex: 1,
      fromTransformResult: 1
    }
  ],
  tooltip: {
    trigger: 'item',
    axisPointer: {
      type: 'shadow'
    }
  },
  grid: {
    left: '10%',
    right: '10%',
    bottom: '15%'
  },
  xAxis: {
    type: 'category',
    boundaryGap: true,
    nameGap: 30,
    splitArea: {
      show: false
    },
    splitLine: {
      show: false
    }
  },
  yAxis: {
    type: 'value',
    name: 'km/s minus 299,000',
    splitArea: {
    show: true
    }
  },
  series: [
    {
      name: 'boxplot',
      type: 'boxplot',
      datasetIndex: 1
    },
    {
      name: 'outlier',
      type: 'scatter',
      datasetIndex: 2
    }
  ]
};
```

### 代码解析

在上述的Echarts选项对象(`option`)中，定义了一些关键的配置项以创建盒须图，具体包括：

- `title`：配置图表的标题信息，包括主标题以及副标题。
- `dataset`：定义数据源，第一项为原始数据，第二项通过转换类型`boxplot`用于生成盒须图需要的五数概括，第三项用于生成散点图的异常值数据。
- `tooltip`：配置工具提示，以展示每个数据点的具体信息。
- `grid`：设置网格的位置，为了让图表在容器中更好地显示。
- `xAxis`与`yAxis`：配置x轴和y轴的相关属性，如类型、边距、分隔区域等。
- `series`：配置数据系列，包含盒须图和散点图的设置。

#### 数据準备

在`dataset`的第一个对象中，`source` 数组内包含了几个子数组，每个子数组就代表一组待绘制的盒须图的数据。

#### 数据转换

在上面的代码中，`transform` 对象对原始数据进行了盒须图的五数概括计算。通过`boxplot`的转换类型，可自动计算出上四分位数、中位数、下四分位数等统计量，并在`dataset`的第二项中生成新的数据结构以供盒须图使用。

#### 坐标轴配置

盒须图的x轴是分类的，每一个数据集为一个分类；y轴则是数值类型，表示数值的大小。

#### 系列配置

在`series`数组中，定义了两个系列，第一个为`boxplot`类型，用以绘制盒须图的主体部分，指定了`datasetIndex`为1，也就是指向经过`boxplot`转换的数据结构；第二个为`scatter`类型，用以标注异常值，指定了`datasetIndex`为2，根据`transform`计算的结果来标注数据中的异常点。