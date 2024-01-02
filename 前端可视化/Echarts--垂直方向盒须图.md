# Echarts--垂直方向盒须图

在数据分析和统计领域，盒须图是一种常用的图表类型，它可以明确地表达出数据的集中趋势和分散程度。与传统的水平盒须图相比，垂直方向盒须图在视觉表达上往往更为直观，特别是在对比多个类别数据时更能突出其差异性。本文将详细介绍如何使用Echarts工具库来创建一个垂直方向的盒须图，并提供一个完整的代码示例。

## 盒须图的数据表示

在介绍绘制方法之前，先对盒须图的结构和其代表的数据做一简要说明。盒须图通过一个矩形盒子（箱体）和两条线（须）来表示数据。箱体表示从下四分位数（Q1）到上四分位数（Q3）的范围，矩形盒子中的横线通常表示中位数（Q2）。而两条线则表示数据的正常区间，通常定为Q1-1.5倍的四分位距（IQR）和Q3+1.5倍的IQR，任何在此范围外的数据点都被视为异常值。

## Echarts 配置项说明

在Echarts中，创建垂直方向盒须图涉及到的配置项主要有以下几个方面：

- `title`：图表的标题配置，可设置主副标题。
- `dataset`：数据集，其中包含原始数据和转换产生的boxplot数据。
- `tooltip`：提示框配置，用于指导用户了解数据详细信息。
- `grid`：直角坐标系内绘图网格。
- `yAxis`和`xAxis`：坐标轴配置，这里的yAxis配置为类目型数据以适应垂直方向的盒须图。
- `series`：包含了盒须图和异常点标记的数据系列类型配置。

## Echarts 垂直方向盒须图代码示例

以下是使用Echarts绘制一个垂直方向盒须图的详细代码：

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
        fontSize: 14
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
        config: {
          itemNameFormatter: function (params) {
            return 'expr ' + params.value;
          }
        }
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
  yAxis: {
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
  xAxis: {
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
      encode: { x: 1, y: 0 },
      datasetIndex: 2
    }
  ]
};
```

### 代码详细解析

1. `dataset`对象定义了三个子项：第一个子项存放原始数据，即各组实验的测量结果；第二个子项使用`transform`配置项对原始数据进行盒形图的统计数据转换，方便后续图表的使用；最后一个子项从统计数据中提取异常情况的数据点。`itemNameFormatter`函数的使用是为了自定义每个盒须的类别名称。

2. `tooltip`是配置浮动提示框的显示内容，此处的`trigger: 'item'`表示鼠标悬停到数据项上时才显示提示。

3. `grid`是图表的网格配置，这里主要是确定了盒须图在画布中的位置。

4. `yAxis`和`xAxis`进行了轴向设置的交换，对应到垂直盒须图的布局，即类目轴（`category`）用于数据类别，在垂直方向展示；数值轴（`value`）用于数据的值，横向展示。

5. 在`series`中，定义了两类系列数据，第一类`boxplot`用于绘制盒须图主体，第二类`scatter`则用于绘制异常值。对于`scatter`系列，`encode`配置项指定了数据应该如何映射到图表的x轴和y轴，实现自定义的数据展现。

通过以上代码和配置，可以生成一个清晰直观的垂直方向盒须图，优势在于可以同时展示多个类别的数据分布，非常适用于多样本的对比分析。Echarts以其强大的配置和自定义能力，使得数据可视化变得简单而高效。