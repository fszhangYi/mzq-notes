# ECharts--可滚动的图例

ECharts 是一款由百度开源的数据可视化工具，它提供丰富的图表类型，灵活的配置选项，以及良好的交云性来满足各种数据可视化需求。随着数据量的增大，当一个图表中包含大量类目时，为了更好的展示效果及用户体验，可滚动的图例（legend）功能应运而生，让图表更加整洁且不失信息量。

本文中将介绍如何使用 ECharts 创建包含可滚动图例的图表，并提供具体的代码示例以及解释如何生成数据和配置相关的选项。

## 示例介绍

下面是一个针对可滚动图例的简单示例，该示例展示了一个包含 50 个数据项的饼图。在大量的数据项存在的情况下，让图例支持滚动是提高用户交云性的好方法。

```javascript
const data = genData(50); // 生成图例和系列数据
option = {
  title: { // 图表标题
    text: '同名数量统计',   
    subtext: '纯属虚构',
    left: 'center'
  },
  tooltip: { // 提示框组件
    trigger: 'item',
    formatter: '{a} <br/>{b} : {c} ({d}%)'
  },
  legend: { // 图例组件
    type: 'scroll', // 设置为可滚动的图例
    orient: 'vertical', // 图例列表的布局朝向
    right: 10,
    top: 20,
    bottom: 20,
    data: data.legendData // 初始化图例数据
  },
  series: [ // 系列列表
    {
      name: '姓名',
      type: 'pie', // 饼图
      radius: '55%', // 饼图的半径
      center: ['40%', '50%'], // 饼图的中心（水平，垂直位置）
      data: data.seriesData, // 系列数据
      emphasis: {
        itemStyle: {
          shadowBlur: 10,
          shadowOffsetX: 0,
          shadowColor: 'rgba(0, 0, 0, 0.5)' // 高亮样式
        }
      }
    }
  ]
};

function genData(count) { // 生成模拟数据的函数
  // 省略部分代码...
}
```

## genData 函数详解

genData 函数的作用是生成模拟的图例数据和系列数据。通过传入参数 `count` 来控制生成数据的数量。对于每一个数据项，我们生成了一个随机的名字并给它随机的值。其中，`makeWord` 函数用于生成随机的中文名字字符。

## 配置可滚动的图例

在 `legend` 配置项中设置 `type` 为 `'scroll'` 来启用图例的滚动功能。另外，通过 `orient` 选项可以设置图例的排列方向，`'vertical'` 表示垂直排列，`'horizontal'` 则表示水平排列。通过在配置项中指定 `top`、`right`、`bottom`、和 `left` 等属性，可以控制图例的位置。

```javascript
legend: {
  type: 'scroll', // 启用滚动条
  orient: 'vertical', // 垂直显示
  right: 10, // 距离容器右侧的距离
  top: 20, // 距离容器顶部的距离
  bottom: 20, // 距离容器底部的距离
  data: data.legendData // 指定图例的数据
}
```

在 `series` 的 `data` 属性中，为每一个 `name` 分配一个 `value`，这些 `name` 将会被用作饼图每一块的名称并在图例中显示。每一块的值（或大小）由 `value` 决定。

当配置完成后，图例超过容器大小时将自动启用滚动条。用户可以使用鼠标滚轮滚动图例，或点击图例旁的上/下箭头来查看更多的类目项。

## 实现高亮效果

在饼图的 `emphasis` 配置中，可以设置当鼠标悬浮在饼图的某一块上时，该块的高亮样式。

```javascript
emphasis: {
  itemStyle: {
    shadowBlur: 10,
    shadowOffsetX: 0,
    shadowColor: 'rgba(0, 0, 0, 0.5)'
  }
}
```

`shadowBlur` 控制高亮时的阴影模糊大小，`shadowOffsetX` 和 `shadowOffsetY` 相对应地控制阴影的横向和纵向偏移，`shadowColor` 则指定了阴影的颜色。

## 总结

本文详细介绍了如何在 ECharts 中创建可滚动的图例，并通过一个具体的饼图案例展示了实现的方法和步骤。通过可滚动图例，能够有效地展示大量的数据项，同时保持图表的清晰和美观，为用户提供了更佳的查看体验。开发者在设计大规模数据展示的图表时，应充分考虑可滚动图例的使用，以增强数据可视化的适应性和互动性。

在阅读上述示例和解释后，希望读者能够掌握使用 ECharts 实现可滚动图例的技巧，并将其应用到实际的项目开发中。此外，ECharts 提供的功能不止于此，通过阅读官方文档和资源，可以进一步拓展关于 ECharts 的知识与技能。