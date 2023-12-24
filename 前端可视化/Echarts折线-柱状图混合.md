# Echarts折线-柱状图混合

在众多数据可视化需求中，折线图和柱状图是最基本同时也是最常用的图表类型。这两种图表在表达数据趋势和类别对比方面各有优势。然而，在实际应用中，往往需要将两者结合起来共同展现，以提供更多维度的数据解释。Echarts提供了强大的折线-柱状图混合功能，可以在同一个图表内同时显示柱状图和折线图，此举能够让数据分析更加全面和直观。

## 折线图与柱状图特点

- **折线图**：
  - 擅长展示数据随时间或顺序变化的趋势。
  - 适合表现连续性数据。
  - 清晰地看出数据波动的幅度。

- **柱状图**：
  - 易于比较不同分组之间的数值大小。
  - 显示分类数据。
  - 直观展现数值的大小差异。

折线-柱状图混合利用了这两种图表的优势，使得在同一视图中能够更有效地对比和分析数据。

## Echarts折线-柱状图混合配置

配置一个Echarts折线-柱状图混合图表需要对多个关键组件进行设置，包括工具提示（tooltip）、工具盒（toolbox）、图例（legend）、横轴（xAxis）、纵轴（yAxis）以及系列（series）等。接下来将通过一个具体的示例，详细介绍每个组件的配置方法和应用。

### 基础配置示例代码

以下是一个基础的折线-柱状图混合配置代码：

```js
option = {
  tooltip: {
    trigger: 'axis',
    axisPointer: {
      type: 'cross',
      crossStyle: {
        color: '#999'
      }
    }
  },
  toolbox: {
    feature: {
      dataView: { show: true, readOnly: false },
      magicType: { show: true, type: ['line', 'bar'] },
      restore: { show: true },
      saveAsImage: { show: true }
    }
  },
  legend: {
    data: ['Evaporation', 'Precipitation', 'Temperature']
  },
  xAxis: [
    {
      type: 'category',
      data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
      axisPointer: {
        type: 'shadow'
      }
    }
  ],
  yAxis: [
    {
      type: 'value',
      name: 'Precipitation',
      min: 0,
      max: 250,
      interval: 50,
      axisLabel: {
        formatter: '{value} ml'
      }
    },
    {
      type: 'value',
      name: 'Temperature',
      min: 0,
      max: 25,
      interval: 5,
      axisLabel: {
        formatter: '{value} °C'
      }
    }
  ],
  series: [
    {
      name: 'Evaporation',
      type: 'bar',
      tooltip: {
        valueFormatter: function (value) {
          return value + ' ml';
        }
      },
      data: [
        2.0, 4.9, 7.0, 23.2, 25.6, 76.7, 135.6, 162.2, 32.6, 20.0, 6.4, 3.3
      ]
    },
    {
      name: 'Precipitation',
      type: 'bar',
      tooltip: {
        valueFormatter: function (value) {
          return value + ' ml';
        }
      },
      data: [
        2.6, 5.9, 9.0, 26.4, 28.7, 70.7, 175.6, 182.2, 48.7, 18.8, 6.0, 2.3
      ]
    },
    {
      name: 'Temperature',
      type: 'line',
      yAxisIndex: 1,
      tooltip: {
        valueFormatter: function (value) {
          return value + ' °C';
        }
      },
      data: [2.0, 2.2, 3.3, 4.5, 6.3, 10.2, 20.3, 23.4, 23.0, 16.5, 12.0, 6.2]
    }
  ]
};
```

### 配置解读

在上述配置中，`tooltip`属性设定了鼠标悬停时的交互行为。通过配置`axisPointer`的`type`为`cross`，在悬停时可显示交叉型指示器，增强了交互的直观性。

`toolbox`属性为用户提供了一系列实用工具，如数据视图、切换图表类型、图表还原及保存为图片等功能，增强了图表的易用性和互动性。

`legend`组件显示了三个系列的名称，用户可以通过它来选择要显示的数据系列。

`xAxis`配置了一个分类横轴，包含周一到周日的数据类别。`axisPointer`的`shadow`类型提供了一个跟随鼠标移动的指示阴影。

`yAxis`配置了两个纵轴，第一个用于显示降水量（Precipitation），第二个用于显示温度（Temperature）。通过`formatter`属性设置了纵轴标签的格式。

`series`数组包含了三个系列，每个对应一种类型的数据。`Evaporation`和`Precipitation`以柱状图展示，`Temperature`则以折线图展示。折线图中使用了`yAxisIndex`属性来指定使用的是第二个纵轴。

此配置建立了一个以时间为横轴的多维数据展示图表。在图表中，降水量和蒸发量以柱状图的形式呈现，而温度则以线图形式展现在次轴上。通过这种混搭方式，图表允许用户沿着时间轴同时查看并比较三类数据指标。

## Echarts集成与实现

为了在网页上实现所配置的折线-柱状图混合图表，开发者需要在HTML文件中创建一个用于容纳图表的DOM元素，并在JavaScript中引入Echarts库，然后使用以上配置初始化图表。