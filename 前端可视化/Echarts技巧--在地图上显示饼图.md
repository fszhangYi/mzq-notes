# Echarts技巧--在地图上显示饼图

在多维度数据的可视化处理中，地图与饼图的结合使用可以提供地理信息与分类数据的联合展示。在ECharts中，利用地理坐标系与饼图系列，可以在地图上精准地定位并展示复杂的数据结构。本文将探讨如何在ECharts中实现在地图上显示饼图，并通过案例详细介绍其配置和实现方法。

## 地图和饼图的结合意义

在数据可视化领域，地图用来表达具有地理属性的数据，而饼图则常用来展现数据的分类比例。两者结合，能够实现区域性数据分类比例的直观呈现，既显示了地域分布，又清晰表达了各个区域的数据构成，适用于多种场合如销售额分布、选举结果分析、人口结构研究等。

## 饼图展现在地图上的思路

在ECharts中实现地图上饼图的关键点在于：

1. 使用地理坐标系`geo`作为容器来放置地图。
2. 在地理坐标系中通过饼图系列`pie`展现数据，设置`coordinateSystem`属性为`geo`。
3. 利用地理坐标或地区名称确定饼图在地图上的位置。
4. 使用接口从服务器获取地图数据，并在数据载入后构建视图。

## 实现地图上饼图的步骤

### 1. 导入地图数据

在ECharts中绘制地图前，首先需要导入地区的地图数据。如下所示，Ajax从服务器获取美国地图数据，并注册地图:

```javascript
$.get(ROOT_PATH + '/data/asset/geo/USA.json', function (usaJson) {
  echarts.registerMap('USA', usaJson, {
    // 对于较大或多个离岛区域的特殊处理
    'Alaska': { left: -131, top: 25, width: 15 },
    'Hawaii': { left: -110, top: 28, width: 5 },
    'Puerto Rico': { left: -76, top: 26, width: 2 }
  });
  // 配置图表选项和数据
  var option = {/* ... */};
  myChart.hideLoading();
  myChart.setOption(option);
});
```

这段代码中，美国地图数据载入后注册为名为'USA'的地图。可以对其中特殊地区做一些位置调整，以便更好地展示。

### 2. 准备饼图数据

为每个饼图准备数据，这里通过`randomPieSeries`函数生成饼图数据：

```javascript
function randomPieSeries(center, radius) {
  // 生成随机数据
  const data = ['A', 'B', 'C', 'D'].map((t) => {
    return {
      value: Math.round(Math.random() * 100),
      name: 'Category ' + t
    };
  });
  // 返回饼图系列配置
  return {
    type: 'pie',
    coordinateSystem: 'geo', // 使用geo坐标系
    radius: radius, // 饼图尺寸
    center: center, // 饼图位置
    data: data // 数据项
  };
}
```

该函数针对每个饼图，根据传递的地理位置`center`和尺寸`radius`生成配置。

### 3. 配置ECharts选项

在饼图数据准备好之后，接下来配置ECharts选项：

```javascript
option = {
  geo: {
    map: 'USA',
    roam: true, // 允许拖动和缩放
    itemStyle: { areaColor: '#e7e8ea' } // 地图区域的样式
  },
  tooltip: {}, // 配置提示框组件
  legend: {}, // 配置图例组件
  series: [ // 系列列表
    randomPieSeries([-86.753504, 33.01077], 15),
    randomPieSeries([-116.853504, 39.8], 25),
    randomPieSeries([-99, 31.5], 30),
    randomPieSeries('Maine', 12) // 也可以使用地区名称定位
  ]
};
```

在`series`数组中，每个元素表示一个饼图。饼图通过`randomPieSeries`函数生成，定位到地图的具体经纬度或者地区名称上。

### 4. 应用ECharts配置并渲染地图

最后一步是将配置应用到ECharts实例并显示结果。

```javascript
myChart.hideLoading();
myChart.setOption(option);
```

整个地图加载完成后，隐藏加载提示，并且使用 `setOption` 方法设置配置选项来绘制地图和饼图。

## 总结

结合地图与饼图在ECharts中进行可视化展示，能够为分析提供更为丰富和直观的地理数据信息。文章通过具体的代码示例，详细说明了在地图上显示饼图的过程，包括地图数据的导入与注册，饼图数据的准备和配置，以及最终的图表渲染。通过这种方式，可以有效地将数据的地理分布特征和分类结构特征相结合，对数据进行全方位的展示和分析。