封装一个名为ListSelect的组件，其作用为从后端接口获取数据然后渲染成选项。同时，支持国际化和禁用；它是一个受控组件，受控体现在Select组件的onChange和value属性上面。

### 接口定义

- `IItem`：选项格式的接口类型，包含`id`和`value`属性。
- `IProps`：组件的props格式，包含`value`、`handleChange`和`disabled`属性。
- `IResult`：接口返回格式的类型，包含`data`和`success`属性。

### 组件构造

- 使用`useState`定义一个`source`变量，用于保存Select的原始数据。
- 使用`useEffect`钩子在组件挂载和更新时发送网络请求，并使用`setState`更新`source`的值。
- 在组件返回值中使用Ant Design的Select组件，并传入属性：
  - `onChange`：绑定`props.handleChange`回调函数，用于选项变化时通知父组件。
  - `disabled`：绑定`props.disabled`，表示是否禁用。
  - `placeholder`：使用`getIntl`方法获取国际化的占位符。
  - `value`：使用`props.value`，表示当前选中的值。
- 使用`source`数组的`map`方法生成Option组件，每个Option组件的值和展示内容分别使用`item.id`和`item.value`。

### 组件内容

```tsx
import React, { useState, useEffect } from 'react';
import { Select } from 'antd';
import { request, getIntl } from '../utils';
const { Option } = Select;

// 选项格式
type IItem = {
  id:string;
  value:string;
}

// props格式
interface IProps {
  value: string,
  handleChange: (e:any)=>any,
  disabled: boolean,
}

// 接口返回格式
interface IResult {
  data: Array<IItem>,
  success: boolean,
}

// 封装的组件
const ListSelect = (props: IProps) => {
  // 渲染Select的原始数据
  const [source, setSource] = useState<IItem[]>([]);

  // 在useEffect中发起网络请求，并在回调中通过setState更新列表值
  useEffect(() => {
    request.get('/**/*/list').then((res: IResult) => {
      const { data, success } = res;
      if (success) {
        setSource(data);
      }
    });
  }, []);

  // 渲染Select
  return (
    <Select
      onChange={props?.handleChange} // 选中后通知调用者的回调
      disabled={props.disabled} // 由调用者决定是否可用
      placeholder={getIntl('Common.PleaseSelect')} // 国际化的占位符
      value={props.value} // 组件受控，使用onChange和value搭配之后的数据流向为：选项被选中，触发onChange进而触发传入的handleChange引起调用组件中相应值变化，导致传入的value值发生变化，组件重新渲染
    >
      {source &&
        source.length > 0 &&
        source.map(item => {
          return (
            <Option value={item.id} key={item.value}>
              {item.value}
            </Option>
          );
        })}
    </Select>
  );
};

export { ListSelect };
```
