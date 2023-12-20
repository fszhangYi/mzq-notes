# Echarts动态排序折线图

在数据可视化领域，折线图是表现时间序列数据变化的一种常见图表类型。Echarts，作为一个使用广泛的JavaScript图表库，提供了创建动态排序折线图的强大功能。动态排序折线图能够展现多个序列随时间的动态变化并且相互比较，非常适用于展示多个维度随时间的变化趋势。本文将首先介绍如何使用Echarts创建此类图表，并在此基础上展开Echarts折线图渐变功能的使用。

## 动态排序折线图的创建过程

在展现多个国家或地区随时间变化的数据时，动态排序折线图是一种十分直观的表现形式。以下是创建一个以国家收入为例的排序折线图的步骤和代码。

### 初始数据获取

首先通过异步加载的方式获取用于展示的原始数据，这里假设从一个JSON文件中通过GET请求取得数据。

```javascript
$.get(ROOT_PATH + '/data/asset/data/life-expectancy-table.json', function (_rawData) {
  run(_rawData);
});
```

### 数据集的处理

定义一个函数`run`，将获取到的原始数据作为参数。在`run`函数中，首先创建一个国家或地区的列表。这些将是图表中要显示的维度。

```javascript
const countries = [
  'Finland',
  'France',
  'Germany',
  'Iceland',
  'Norway',
  'Poland',
  'Russia',
  'United Kingdom'
];
```

随后为每个国家创建一个过滤后的数据集，并推送到`datasetWithFilters`数组中。每个数据集包含了过滤条件以及一些其他必要的配置。

```javascript
const datasetWithFilters = [];
echarts.util.each(countries, function (country) {
  var datasetId = 'dataset_' + country;
  datasetWithFilters.push({
    id: datasetId,
    fromDatasetId: 'dataset_raw',
    transform: {
      type: 'filter',
      config: {
        and: [
          { dimension: 'Year', gte: 1950 },
          { dimension: 'Country', '=': country }
        ]
      }
    }
  });
});
```

### 系列的配置

对于每一个国家或地区的系列配置，包括线的类型（此处为折线图），数据集`datasetId`以及一些针对特定系列的样式设置。

```javascript
const seriesList = [];
echarts.util.each(countries, function (country) {
  var datasetId = 'dataset_' + country;
  seriesList.push({
    // 系列配置...
  });
});
```

### Echarts配置项设置

完成了数据集处理和系列配置之后，就需要设置Echarts的配置项`option`。`option`对象包含了Echarts图表所需的全部配置，包括标题、工具提示、坐标轴、网格和系列。

```javascript
option = {
  // Echarts配置项...
};
```

最后，将配置项`option`设置到Echarts实例`myChart`上。

```javascript
myChart.setOption(option);
```

## 折线图渐变效果的使用

在以上动态排序折线图的基础上，Echarts还提供了丰富的样式设置，包括给折线图添加渐变效果，这样可以使图表更加美观且具有立体感。

### 设置渐变色

在Echarts中设置渐变色可以通过定义一个线性渐变对象来实现。该对象可以在系列的`areaStyle`或者`itemStyle`属性中指定。

```javascript
const gradient = new echarts.graphic.LinearGradient(0, 0, 0, 1, [{
  offset: 0,
  color: 'rgba(255,0,0,1)' // 0% 处的颜色
}, {
  offset: 1,
  color: 'rgba(255,0,0,0)' // 100% 处的颜色
}]);
```

此处创建了一个从红色到透明的垂直渐变对象`gradient`。

### 应用渐变效果

将渐变对象应用到图表的系列配置中。

```javascript
seriesList.push({
  // 系列其他配置...
  areaStyle: {
    normal: {
      color: gradient // 使用渐变色
    }
  }
});
```

在`seriesList`数组里推送系列时，为`areaStyle`属性添加上述渐变对象，从而使得折线图区域呈现出渐变效果。

### 完整的Echarts配置项示例

以下是具有渐变效果的完整Echarts配置项的示例。

```javascript
option = {
  // 其他配置项...
  series: seriesList.map(function (series) {
    return echarts.util.extend(series, {
      areaStyle: {
        normal: {
          color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [{
            offset: 0,
            color: 'rgba(255,0,0,1)'
          }, {
            offset: 1,
            color: 'rgba(255,0,0,0)'
          }])
        }
      }
    });
  })
};
```

### 结论

折线图是展示时间序列数据变化的重要工具。当涵盖动态排序及视觉效果时，它不仅提供了信息显示的功能，还能提升用户的视觉体验。Echarts库为开发者提供了创建这样的图表的大量配置选项和样式自定义能力。渐变效果的添加，能有效吸引观众的注意力，使得数据展现更具表现力和美感。通过合理的运用Echarts的功能，可以打造出既美观又具有实用性的数据可视化图表。