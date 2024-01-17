最近写了大量的Form表单，不是一个接一个，而是每隔几天就来一个那种。每次都会卡在Modal的footer上面。我已经受够了，既然记不住怎么用，我就不打算记了，索性写在博客上，每次用都来查一下，也是极好的。

如题，就是使用Modal弹出一个Form，使用的都是antd中的组件，为了能够自定义footer，要分成下面几个步骤：

## 步骤0：创建一个form handler
```jsx
    import { form } from 'antd';
    ...
    const [form] = Form.useForm();
```

## 步骤1：给Form绑定handler
```jsx
        <Form
          form={form}
          ...
```

## 步骤2：Modal打开或者关闭的时候重置表单数据
注意：如果需要回填数据，则会在其他useEffect中使用setFieldsValue进行回填。
```jsx
  useEffect(() => {
    form.resetFields();
  }, [props?.visible]);
```

## 步骤3：在Modal的footer中自定义button
```jsx
    <Modal
      forceRender
      visible={visible}
      ...
      footer={[
        <Button onClick={outerModalCancel}>Back</Button>,
        <Button type="primary" onClick={() => form.submit()}>
          Submit
        </Button>
      ]}
      ...
```

由于本人之前搞得不是互联网，所以最近才开始大量使用antd组件，写的不对的地方还请各位大佬多多包涵。
