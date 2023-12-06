本文介绍了一种封装antd的Tab组件的方法。通过对antd的Tab组件的封装，可以让我们更加聚焦在每个页面的逻辑构造上，从而提高开发效率。

## 1. 首先，引入需要的依赖项和样式文件

```javascript
import './index.less';
import React, { useState, useEffect } from 'react';
import PageLayout from '../components/PageLayout';
import {getData} from '../utils/api/';
import { Tabs } from 'antd';
import Page1 from './Page1';
import Page2 from './Page2';
import Page3 from './Page3';
import Page4 from './Page4';
import PropTypes from 'prop-types';
```

以上代码段中，开始引入了必要的样式文件和依赖项。`index.less`用于添加组件特定样式。还引入了React相关的函数和组件，包括自定义的`PageLayout`、`Tabs`以及Page1到Page4四个页面组件。最后，引入了`PropTypes`用于对属性进行类型检查。

## 2. 定义 ViewMgt 组件，接受一个名为 props 的参数

```javascript
export default function ViewMgt(props) {
  const [activeKey, setActiveKey] = useState('page1');
  const [allData, setAllData] = useState({
    'page1': null，
    'page2': null，
    'page3': null，
    'page4': null，
  });
  const [flag, setFlag] = useState(true);

  useEffect(() => {
    const { activeTab } = (props && props.location.state) || {};
    setActiveKey(activeTab || 'page1');

    getData().then(data => {
        setAllData({...data})
    })
  }, []);

  const forceUpdate(setFlag(!flag));
```

以上代码段中，定义了一个名为`ViewMgt`的函数组件，它接受一个名为`props`的参数。

在组件内部，使用了`useState`和`useEffect` Hook。`useState`用于定义具有初始值`'page1'`的状态变量`activeKey`和对应的状态更新函数`setActiveKey`。`useEffect`用于在组件挂载时根据`props.location.state`中的`activeTab`设置初始的选项卡活动状态。

## 3. 定义选项卡的配置数组

```javascript
const items = [
  {
    key: 'page1',
    label: 'Page1',
    component: <Page1 forceUpdate={forceUpdate} data={allData} />,
  },
  {
    key: 'page2',
    label: 'Page2',
    component: <Page2 forceUpdate={forceUpdate} data={allData} />,
  },
  {
    key: 'page3',
    label: 'Page3',
    component: <Page3 forceUpdate={forceUpdate} data={allData} />,
  },
  {
    key: 'page4',
    label: 'Page4',
    component: <Page4 forceUpdate={forceUpdate} data={allData} />,
  },
];
```

以上代码段中，定义了一个名为`items`的数组来配置选项卡。

数组中的每个对象分别代表一个选项卡，包含了`key`、`label`和`component`属性。`key`是选项卡的键，`label`是选项卡的标签，`component`是对应的页面组件，通过JSX语法进行渲染。

## 4. 构造组件

```javascript
return (
  <PageLayout
    className="Your page's root classname."
    title="Your page's title."
    desc="Your page's description."
  >
    <Tabs id={'your-page-tabs'} activeKey={activeKey} onChange={setActiveKey}>
      {items.map(pane => (
        <TabPane tab={pane.label} key={pane.key}>
          <div className="your-page-pane">{pane.component}</div>
        </TabPane>
      ))}
    </Tabs>
  </PageLayout>
);
```

以上代码段中，返回了组件的 JSX 结构。

在此结构中，使用了`PageLayout`组件作为页面的布局容器，并传入了`className`、`title`和`desc`作为属性。

在`Tabs`组件中，设置了`id`、`activeKey`和`onChange`属性，`activeKey`使用了之前定义的`activeKey`状态变量，`onChange`使用了之前定义的`setActiveKey`状态更新函数。

在`Tabs`组件内部，使用`map`方法遍历`items`数组，为每个选项卡渲染对应的`TabPane`组件。在`TabPane`组件中，渲染了选项卡的内容，通过`pane.component`来引用相应的页面组件。

## 5. 对传入的 props 进行类型检查

```javascript
ViewMgt.propTypes = {
  location: PropTypes.any,
};
```


在`propTypes`中，指定了`location`属性的类型为`any`，这样可以确保在使用`ViewMgt`组件时传递的`props`中`location`属性具有正确的类型。