# echarts-react代码解析
## 构建思路
本文对一段代码进行解析，这段代码是一个基于React和echarts的图表组件，通过封装echarts的相关功能和组件，实现了一个自定义的图表组件。它主要通过React的各个生命周期钩子函数，来初始化、渲染和处理图表的各种操作。
## 步骤详情
1. 导入React相关的组件和hooks，以及echarts相关的组件和功能。
2. 定义了一个MyChartOption类型，用于存储echarts的配置项。
3. 定义了一个MyChartProps接口，用于传递图表组件的属性。
4. 定义了一个MyChartRef接口，用于暴露图表实例的方法。
5. 定义了一个MyChartInner组件，它是一个函数式组件，并通过React.forwardRef函数封装成可以传递ref的组件。
6. 在MyChartInner组件内部，通过useRef钩子函数创建了一个cRef变量，用于引用图表组件的DOM元素。
7. 通过useRef钩子函数创建了一个cInstance变量，用于引用echarts实例。
8. 在MyChartInner组件内部，使用useEffect钩子函数初始化echarts实例，监听cRef和option的变化，并根据条件进行初始化和设置配置项。
9. 使用useEffect钩子函数监听窗口大小的变化，当窗口大小发生变化时，调用resize函数重新调整图表大小。
10. 使用useLayoutEffect钩子函数监听宽度和高度的变化，当宽度和高度发生变化时，调用resize函数重新调整图表大小。
11. 定义了一个resize函数，用于调整图表的大小，并开启过渡动画。
12. 定义了一个instance函数，用于返回图表实例。
13. 使用useImperativeHandle钩子函数，将instance函数暴露给父组件。
14. 在返回的JSX中，将cRef作为ref属性传递给div元素，设置宽度和高度为100%。
## 方法解释
- useEffect：用于处理副作用，类似于生命周期函数componentDidMount、componentDidUpdate和componentWillUnmount的结合。
- useImperativeHandle：用于向父组件暴露一些方法和属性。
- useRef：用于在函数组件中创建一个可变的引用，类似于class组件中的实例属性。
- useLayoutEffect：类似于useEffect，但会在浏览器执行绘制之前同步调用effect，可以防止视觉上的闪烁。
## 数据流向
- option：父组件通过props传递给子组件的图表配置项。
- width和height：父组件通过props传递给子组件的图表宽度和高度。
- cRef.current：获取图表组件的DOM元素。
- cInstance.current：获取echarts实例。
## 完整代码
```jsx
import React, { ForwardedRef, useEffect, useImperativeHandle, useLayoutEffect, useRef } from 'react';
import * as echarts from 'echarts/core';
import { EChartsType } from 'echarts/core';
import { DatasetComponent, DatasetComponentOption, DataZoomComponent, DataZoomComponentOption, GridComponent, GridComponentOption, LegendComponent, LegendComponentOption, TitleComponent, TitleComponentOption, ToolboxComponent, ToolboxComponentOption, TooltipComponent, TooltipComponentOption, VisualMapComponent } from 'echarts/components';
import { BarChart, BarSeriesOption, LineChart, LineSeriesOption } from 'echarts/charts';
import { UniversalTransition } from 'echarts/features';
import { SVGRenderer } from 'echarts/renderers';
import { ECElementEvent } from 'echarts/types/src/util/types';
// 注册echarts的组件和功能
echarts.use([
  VisualMapComponent,
  DatasetComponent,
  DataZoomComponent,
  GridComponent,
  LegendComponent,
  TitleComponent,
  ToolboxComponent,
  TooltipComponent,
  LineChart,
  BarChart,
  UniversalTransition,
  SVGRenderer,
]);
// 定义图表的配置项类型
export type MyChartOption = echarts.ComposeOption<| DatasetComponentOption
  | DataZoomComponentOption
  | GridComponentOption
  | LegendComponentOption
  | TitleComponentOption
  | ToolboxComponentOption
  | TooltipComponentOption
  | LineSeriesOption
  | BarSeriesOption>;
// 定义图表组件的属性类型
export interface MyChartProps {
  option: MyChartOption | null | undefined | any;
  width?: number | string;
  height?: number | string;
  merge?: boolean;
  loading?: boolean;
  empty?: React.ReactElement;
  style?: any
  onClick?(event: ECElementEvent): any;
}
// 定义图表组件的引用类型
export interface MyChartRef {
  instance(): EChartsType | undefined;
}
const MyChartInner: React.ForwardRefRenderFunction<MyChartRef, MyChartProps> = (
  { option, width, height = false, onClick },
  ref: ForwardedRef<MyChartRef>
) => {
  const cRef = useRef<HTMLDivElement>(null);
  const cInstance = useRef<EChartsType>();
  // 初始化注册组件，监听 cRef 和 option 变化
  useEffect(() => {
    if (cRef.current) {
      // 校验 Dom 节点上是否已经挂载了 ECharts 实例，只有未挂载时才初始化
      cInstance.current = echarts.getInstanceByDom(cRef.current);
      if (!cInstance.current) {
        cInstance.current = echarts.init(cRef.current, undefined, {
          renderer: 'svg',
        });
        cInstance.current.on('click', (event) => {
          const ec = event as ECElementEvent;
          if (ec && onClick) onClick(ec);
        });
      }
      // 设置配置项
      if (option) cInstance.current?.setOption(option);
    }
  }, [cRef, option]);
  // 监听窗口大小变化重绘
  useEffect(() => {
    window.addEventListener('resize', resize);
    return () => {
      window.removeEventListener('resize', resize);
    };
  }, [option]);
  // 监听高度变化
  useLayoutEffect(() => {
    resize();
  }, [width, height]);
  // 重新适配大小并开启过渡动画
  const resize = () => {
    cInstance.current?.resize({
      animation: { duration: 300 }
    });
  }
  // 获取实例
  const instance = () => {
    return cInstance.current;
  }
  // 对父组件暴露的方法
  useImperativeHandle(ref, () => ({
    instance
  }));
  return (
    <div ref={cRef} style={{ width: '100%', height: '100%' }} />
  );
};
const lineChart = React.forwardRef(MyChartInner);
export default lineChart;
```
以上就是该代码的完整解释和代码清单。通过这个组件可以方便地在React项目中使用echarts库进行图表的展示和交互。