# Echarts嵌套环形图

---

## 一、嵌套环形图概念

嵌套环形图，又称多层饼图，是一种通过环形的层次结构来展示数据关系的图表类型。在 Echarts 中，该图表通过多个饼图系列的半径设置来实现层次分明的数据展示效果。该类型的图表适用于展示数据的部分与整体的关系，便于观众直观理解数据的构成。

## 二、嵌套环形图的应用场景

嵌套环形图适用于表现数据的集合与子集之间的关系，例如网站流量的来源分析，可以用外环表示流量的具体来源，内环表示流量来源的大类。此外，嵌套环形图也常用于企业销售数据的分类展现，比如不同产品线的销售占比及各自内部的种类细分等。

## 三、Echarts嵌套环形图的画法

Echarts 绘制嵌套环形图的过程主要包括三个步骤：图表基础配置、内环配置和外环配置。

### 3.1 基础配置

首先要进行的是图表的基础配置，包括提示框和图例的设置。

```javascript
option = {
  tooltip: {
    trigger: 'item',
    formatter: '{a} <br/>{b}: {c} ({d}%)'
  },
  legend: {
    data: [
      // 这里的内容将根据series中的data自动匹配生成
    ]
  },
  series: [
    // series数组中将包含两个对象，分别代表内环和外环的配置
  ]
};
```

### 3.2 内环配置

接着是内环的配置，需设置其`type`属性为`'pie'`，通过`radius`属性定义环的大小，使用`label`属性设置标签的位置和样式。

```javascript
{
  name: 'Access From', // 系列名称，将用于tooltip的显示
  type: 'pie', // 图表类型
  selectedMode: 'single', // 选中模式，single表示只能选中一个扇形
  radius: [0, '30%'], // 半径的两个值分别表示内半径和外半径，百分比是相对于容器高宽中较小的一个值
  label: {
    position: 'inner', // 标签位置
    fontSize: 14
  },
  labelLine: {
    show: false // 不显示标签的引导线
  },
  data: [
    // 数据对象的数组
  ]
}
```

内环通常用于展示一级分类的数据。

### 3.3 外环配置

外环的配置类似于内环，但`radius`的值应设置得更大，以形成嵌套的效果。此外，标签的样式和格式会有更多的自定义，以适应更复杂的显示需要。

```javascript
{
  name: 'Access From',
  type: 'pie',
  radius: ['45%', '60%'], // 外环半径设定为从45%到60%，产生嵌套的效果
  labelLine: {
    length: 30 // 标签引导线的长度
  },
  label: {
    // 更丰富的标签样式设置
  },
  data: [
    // 数据对象的数组
  ]
}
```

外环可以用来展示细分数据或者二级分类。

## 四、详细代码实现

以下是一个完整的 Echarts 嵌套环形图的代码实现示例。

```javascript
option = {
  tooltip: {
    trigger: 'item',
    formatter: '{a} <br/>{b}: {c} ({d}%)'
  },
  legend: {
    data: [
      'Direct',
      'Marketing',
      'Search Engine',
      'Email',
      'Union Ads',
      'Video Ads',
      'Baidu',
      'Google',
      'Bing',
      'Others'
    ]
  },
  series: [
    {
      name: 'Access From',
      type: 'pie',
      selectedMode: 'single',
      radius: [0, '30%'],
      label: {
        position: 'inner',
        fontSize: 14
      },
      labelLine: {
        show: false
      },
      data: [
        { value: 1548, name: 'Search Engine' },
        { value: 775, name: 'Direct' },
        { value: 679, name: 'Marketing', selected: true }
      ]
    },
    {
      name: 'Access From',
      type: 'pie',
      radius: ['45%', '60%'],
      labelLine: {
        length: 30
      },
      label: {
        formatter: '{a|{a}}{abg|}\n{hr|}\n  {b|{b}：}{c}  {per|{d}%}  ',
        backgroundColor: '#F6F8FC',
        borderColor: '#8C8D8E',
        borderWidth: 1,
        borderRadius: 4,
        rich: {
          a: {
            color: '#6E7079',
            lineHeight: 22,
            align: 'center'
          },
          hr: {
            borderColor: '#8C8D8E',
            width: '100%',
            borderWidth: 1,
            height: 0
          },
          b: {
            color: '#4C5058',
            fontSize: 14,
            fontWeight: 'bold',
            lineHeight: 33
          },
          per: {
            color: '#fff',
            backgroundColor: '#4C5058',
            padding: [3, 4],
            borderRadius: 4
          }
        }
      },
      data: [
        { value: 1048, name: 'Baidu' },
        { value: 335, name: 'Direct' },
        { value: 310, name: 'Email' },
        { value: 251, name: 'Google' },
        { value: 234, name: 'Union Ads' },
        { value: 147, name: 'Bing' },
        { value: 135, name: 'Video Ads' },
        { value: 102, name: 'Others' }
      ]
    }
  ]
};
```

在以上代码中，两个系列配置了内外两个环形图，每个环形图都拥有各自的数据和样式设置。通过智能的布局和丰富的配置选项，Echarts 为数据可视化提供了极大的灵活性和表现力。利用嵌套环形图，可以有效地将数据的层次结构和细节通过视觉元素展现出来，以直观、清晰的方式传达给观众。

## 五、结语

Echarts 的嵌套环形图提供了一种视觉吸引力强，易于理解的数据展示方法。无论是在数据分析、报告展示，还是在商业智能领域，它都是传递多层次数据信息的优秀选择。掌握其配置和使用方法，将有助于在多种场合下更好地利用数据可视化工具，传达复杂的数据关系。