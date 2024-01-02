# Echarts--OHLC 图

## OHLC 图简介

OHLC 图，又称 K 线图，是用来表示股票、金融商品等交易信息的图表。它由一系列的条形线组成，每根线条显示在某个时间段内的开盘价（Open）、最高价（High）、最低价（Low）、收盘价（Close）四个数据点。OHLC 图是金融分析中的一个核心工具，可用于分析市场的行情走势。

## Echarts 中的 OHLC 图绘制

在 Echarts 中绘制 OHLC 图，可以使用 Echarts 提供的 `custom` 系列类型，并通过 `renderItem` 函数自定义渲染逻辑，实现 OHLC 图形的绘制。以下是具体的实现过程及代码示例。

### 数据预处理

OHLC 图的数据通常是由后端提供的原始数据数组，需要先进行处理，以便 Echarts 能够解析。下面的 `splitData` 函数用于将原始数组转换成 Echarts 可以接受的格式。该函数会提取时间数据，并将原始数组的时间转换为序列索引，同时构造一个新的数组用于渲染。

```javascript
function splitData(rawData) {
  const categoryData = [];
  const values = [];
  for (var i = 0; i < rawData.length; i++) {
    categoryData.push(rawData[i][0]);
    rawData[i][0] = i;
    values.push(rawData[i]);
  }
  return {
    categoryData: categoryData,
    values: values
  };
}
```

### 自定义渲染函数

`renderItem` 函数是自定义绘制图形的核心，它定义了如何使用 OHLC 图中的数据点绘制图形。函数接收 `params` 和 `api` 两个参数，通过 `api` 可以获取数据值、计算坐标、获取样式和视觉编码等。

```javascript
function renderItem(params, api) {
  var xValue = api.value(0);
  var openPoint = api.coord([xValue, api.value(1)]);
  var closePoint = api.coord([xValue, api.value(2)]);
  var lowPoint = api.coord([xValue, api.value(3)]);
  var highPoint = api.coord([xValue, api.value(4)]);
  var halfWidth = api.size([1, 0])[0] * 0.35;
  var style = api.style({
    stroke: api.visual('color')
  });

  return {
    type: 'group',
    children: [
      {
        type: 'line',
        shape: {
          x1: lowPoint[0],
          y1: lowPoint[1],
          x2: highPoint[0],
          y2: highPoint[1]
        },
        style: style
      },
      {
        type: 'line',
        shape: {
          x1: openPoint[0],
          y1: openPoint[1],
          x2: openPoint[0] - halfWidth,
          y2: openPoint[1]
        },
        style: style
      },
      {
        type: 'line',
        shape: {
          x1: closePoint[0],
          y1: closePoint[1],
          x2: closePoint[0] + halfWidth,
          y2: closePoint[1]
        },
        style: style
      }
    ]
  };
}
```

### 绘图配置和数据绑定

OHLC 图的绘图配置涉及到 `xAxis`、`yAxis`、`dataZoom` 等多个组件。正确配置这些组件是成功绘制 OHLC 图的关键。

```javascript
$.get(ROOT_PATH + '/data/asset/data/stock-DJI.json', function (rawData) {
  var data = splitData(rawData);
  myChart.setOption({
    animation: false,
    legend: {
      bottom: 10,
      left: 'center',
      data: ['Dow-Jones index']
    },
    tooltip: {
      // ...省略部分配置...
    },
    // ...省略部分配置...
    series: [
      {
        name: 'Dow-Jones index',
        type: 'custom',
        renderItem: renderItem,
        dimensions: ['-', 'open', 'close', 'lowest', 'highest'],
        encode: {
          x: 0,
          y: [1, 2, 3, 4],
          tooltip: [1, 2, 3, 4]
        },
        data: data.values
      }
    ]
  }, true);
});
```

## 实例讲解

本文将继续以股票交易数据为例，展示如何使用上述代码和配置绘制出 OHLC 图。

首先，需确保有一个合适格式的股票交易数据集。通常这类数据包含多个时间点，每个时间点有开盘价、最高价、最低价和收盘价四项数据。数据可以是 JSON 格式，也可以是数组。

然后，利用 `splitData` 函数对数据进行处理，分离出类别数据和数值数据。其中类别数据即横轴上的交易日期或时间，数值数据包含了 `open`、`close`、`low` 和 `high` 四组数据。

接着，通过自定义的 `renderItem` 函数来定义每一笔交易如何显示。在本示例中，定义了不同交易状态的线条颜色，以及如何使用 `api.coord` 方法将数据点映射到坐标系上。

最后，完整的配置选项 `option` 绑定到 Echarts 实例上，通过 `setOption` 方法渲染 OHLC 图。配置中包括了 `tooltip`、`xAxis`、`yAxis` 和 `dataZoom` 等部分，用于提升交互性和图表的清晰度。

以上就是 Echarts OHLC 图的绘制方法和一个具体的代码示例。通过 Echarts 的 `custom` 类型图表和 `renderItem` 函数，用户可以灵活地自定义图表的外观和行为，创造出适合业务需求的交互式金融图表。