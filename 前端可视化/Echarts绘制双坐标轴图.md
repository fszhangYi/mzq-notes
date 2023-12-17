# Echarts绘制双坐标轴图

在数据可视化的过程中，经常会遇到需要同时表现两组数据关系，而这两组数据的量级差异很大或者单位不同，此时，双坐标轴图就成了一种有效的表现形式。Echarts提供了强大功能和灵活配置，使得绘制双坐标轴图变得可行且效果出众。下文将详细介绍如何利用Echarts进行双坐标轴图的绘制，并举例说明细致配置。

## 初始化Echarts实例

首先，确保页面中已经正确引入了Echarts库。这里假设Echarts库已通过CDN链接引入页面。接着，创建一个用于绘图的`<div>`元素，并给它指定一个ID。

```html
<div id="dual-axis-chart" style="width: 800px;height:450px;"></div>
<script src="https://cdn.jsdelivr.net/npm/echarts/dist/echarts.min.js"></script>
<script src="dual-axis.js"></script> <!-- 双坐标轴绘图脚本 -->
```

在`dual-axis.js`文件中，初始化Echarts实例。

```javascript
var chart = echarts.init(document.getElementById('dual-axis-chart'));
```

## 配置选项

接着，定义一个`option`配置对象包含图表的所有配置项。

```javascript
var option = {
    title: {
        text: '温度和湿度变化双坐标轴图'
    },
    tooltip: {
        trigger: 'axis',
        axisPointer: {
            type: 'cross'
        }
    },
    grid: {
        right: '20%'
    },
    toolbox: {
        feature: {
            dataZoom: {
                yAxisIndex: 'none'
            },
            restore: {},
            saveAsImage: {}
        }
    },
    legend: {
        data: ['温度', '湿度']
    },
    xAxis: [{
        type: 'category',
        axisTick: {
            alignWithLabel: true
        },
        data: ['1月', '2月', '3月', '4月', '5月', '6月', '7月', '8月']
    }],
    yAxis: [
        {
            type: 'value',
            name: '温度',
            position: 'left',
            axisLine: {
                lineStyle: {
                    color: '#5793f3'
                }
            },
            axisLabel: {
                formatter: '{value} °C'
            }
        },
        {
            type: 'value',
            name: '湿度',
            position: 'right',
            offset: 80,
            axisLine: {
            lineStyle: {
                color: '#d14a61'
            }
            },
            axisLabel: {
                formatter: '{value} %'
            }
        }
    ],
    series: [
        {
            name: '温度',
            type: 'line',
            yAxisIndex: 0,
            data: [2.0, 4.9, 7.0, 23.2, 25.6, 76.7, 135.6, 162.2]
        },
        {
            name: '湿度',
            type: 'line',
            yAxisIndex: 1,
            data: [26, 59, 90, 264, 287, 707, 1756, 1822]
        }
    ]
};
```

### 配置解析

- `title`：图表的标题。
- `tooltip`：提示框配置，`trigger`属性为`'axis'`表示触发方式为坐标轴触发。
- `grid`：直角坐标系内绘图网格配置，`right`属性确定网格右侧的空间，以便于右侧Y轴的显示。
- `toolbox`：工具箱配置，可以实现诸如数据区域缩放、还原、保存图片等功能。
- `legend`：图例组件，表述了多个系列的图标和名称。
- `xAxis`：类目轴配置，展示横轴信息。
- `yAxis`：数值轴配置，该配置为数组，支持设置多个Y轴。
- `series`：系列列表，每个系列`name`属性与图例相对应，`type`定义为折线图，`yAxisIndex`定义了使用哪个Y轴。

#### Y轴详解

本例中，Y轴是双坐标轴的关键配置：

- 第一个Y轴（温度）定位在左侧，`axisLine`配置对应的轴颜色为蓝色标记温度，`formatter`格式化坐标轴上显示的信息，展示单位为℃。
- 第二个Y轴（湿度）定位在右侧，并且有一个`offset`属性，该属性设定了轴相对于默认位置的偏移量，`formatter`格式化坐标轴上显示的信息，展示单位为%。

#### 系列配置详解

在`series`数组中，两个对象分别定义了两种数据的视觉表示：

- 第一个系列的`yAxisIndex`为0，表示该系列数据使用第一个Y轴（左侧的轴）。
- 第二个系列的`yAxisIndex`为1，表示该系列数据使用第二个Y轴（右侧的轴）。

### 应用配置和渲染

将配置好的图表选项对象应用到之前创建的 Echarts 实例：

```javascript
chart.setOption(option);
```

执行以上代码后，指定的容器中将绘制出含有双坐标轴的折线图。

## 交互增强

Echarts 提供了多种交互功能配置，增强数据的展示效果。

```javascript
option = {
    // ...
    tooltip: {
        trigger: 'axis',
        axisPointer: {
            type: 'cross',
            crossStyle: {
                color: '#999'
            }
        }
    },
    // ...
};
```

通过`axisPointer`配置项，可以启用坐标轴指示器（axisPointer），并设置其类型为`cross`（十字准星指示器），当鼠标悬浮时，十字线可以清晰地跟踪鼠标位置并快速读取数据。

## 自适应性配置

为保证图表大小能够自动适应容器变化，可以添加如下监听器：

```javascript
window.addEventListener('resize', function() {
    chart.resize();
});
```

如此，当浏览器窗口大小变化时，图表会自动适应变化。

## 主题和样式个性化

Echarts支持通过主题定制实现个性化的视觉效果，用户可以选择预定义主题或自定义主题配置。

```javascript
// 应用暗黑主题
var darkChart = echarts.init(document.getElementById('dual-axis-chart'), 'dark');
```

上述代码假设已有一个主题为`dark`的Echarts主题配置文件已经被正确引入。

## 数据动态加载

Echarts 支持图表的动态数据加载与更新，可以通过编写JavaScript函数向后端请求新数据，并通过`setOption`方法对图表进行更新。

```javascript
// 伪代码: 假设从服务器动态获取数据
function fetchData() {
    axios.get('/api/data').then(function (response) {
        chart.setOption({
            series: [{
                // Assuming that response.data is the new data for temperature series
                data: response.data.temperature
            },
            {
                // Assuming that response.data is the new data for humidity series
                data: response.data.humidity
            }]
        });
    });
}
```

## 结语

双坐标轴图在展现相关或对比性强的不同维度数据时，能够提供清晰的视觉辨识度。借助Echarts丰富的配置项和强大的功能，构建符合具体需求的双坐标轴图成为了一件既简便又高效的任务。通过本文的介绍，相信用户已能掌握双坐标轴图的绘制方法，并能进一步探索Echarts提供的更多可能性。