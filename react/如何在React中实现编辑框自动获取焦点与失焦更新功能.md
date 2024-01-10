##### 概述
在React应用中，编辑框的焦点控制和数据回填是一个常见需求。本文将介绍如何使用`useRef`和`useEffect`钩子，在组件中实现输入框自动获取焦点及失焦后更新数据的功能。

##### 实现步骤

1. **引入钩子**: 在组件中引入`useEffect`和`useRef`钩子。
  
2. **创建引用**: 使用`useRef`创建一个引用（`ref`），并将其赋值给输入框的`ref`属性，以便能够访问输入框的DOM元素。

3. **自动获取焦点**: 通过`useEffect`钩子监听`isEditing`状态。当`isEditing`为`true`时，使用引用`ref.current.focus()`让输入框自动获取焦点。

4. **失焦更新数据**: 在输入框的`onBlur`事件中，调用`modifyTodoTitle(ref.current.value)`函数，用于在输入框失去焦点时更新数据。

##### 示例代码

```javascript
import React, { useEffect, useRef } from 'react';

function Editing({ todo }) {
  // 从 todo 对象中解构出 isEditing 状态和 modifyTodoTitle 函数
  const { isEditing, modifyTodoTitle } = todo;

  // 创建一个引用，用于访问输入框 DOM 元素
  const ref = useRef(null);

  // 使用 useEffect 钩子，在 isEditing 状态变为 true 时将焦点设置到输入框
  useEffect(() => {
    if (isEditing) {
      ref.current.focus();
    }
  }, [isEditing]);

  return (
    <input
      ref={ref}
      className="edit"
      defaultValue={todo.title} // 输入框显示待办事项的当前标题
      onBlur={() => modifyTodoTitle(ref.current.value)} // 当输入框失去焦点时，调用 modifyTodoTitle 更新标题
    />
  );
}

export default Editing;
```

##### 功能描述
在上述代码中，`Editing`组件接收一个`todo`对象，其中包含待办事项的标题和编辑状态。使用`useRef`创建一个引用，这样在组件的整个生命周期内都可以持续访问相同的DOM元素。`useEffect`钩子确保在`isEditing`状态为`true`时，输入框能够自动获得焦点。此外，当用户完成编辑并点击其他地方导致输入框失去焦点时，`onBlur`事件触发，调用`modifyTodoTitle`函数来更新待办事项的标题。

---

### English Version

#### How to Implement Auto-Focus and Data Backfilling for Edit Fields in React

##### Overview
Controlling focus and backfilling data in edit fields are common requirements in React applications. This article explains how to use the `useRef` and `useEffect` hooks to implement auto-focus for an input field and update data after it loses focus.

##### Implementation Steps

1. **Import Hooks**: Import `useEffect` and `useRef` hooks in the component.

2. **Create a Reference**: Use `useRef` to create a reference (`ref`) and assign it to the `ref` attribute of the input field to access the input field's DOM element.

3. **Auto-Focus**: Utilize the `useEffect` hook to listen for changes in the `isEditing` state. When `isEditing` is `true`, use the reference `ref.current.focus()` to automatically focus the input field.

4. **Update Data on Blur**: In the `onBlur` event of the input field, invoke the `modifyTodoTitle(ref.current.value)` function to update data when the input field loses focus.

##### Example Code

```javascript
import React, { useEffect, useRef } from 'react';

function Editing({ todo }) {
  // Destructure the isEditing state and the modifyTodoTitle function from the todo object
  const { isEditing, modifyTodoTitle } = todo;

  // Create a ref to access the input box DOM element
  const ref = useRef(null);

  // Use the useEffect hook to set focus to the input box when the isEditing state becomes true
  useEffect(() => {
    if (isEditing) {
      ref.current.focus();
    }
  }, [isEditing]);

  return (
    <input
      ref={ref}
      className="edit"
      defaultValue={todo.title} // Display the current title of the todo item in the input box
      onBlur={() => modifyTodoTitle(ref.current.value)} // Call modifyTodoTitle to update the title when the input box loses focus
    />
  );
}

export default Editing;
```

##### Feature Description
In the code above, the `Editing` component receives a `todo` object, which contains the title of the to-do item and its editing state. A reference is created using `useRef`, allowing continuous access to the same DOM element throughout the component's lifecycle. The `useEffect` hook ensures that the input field automatically receives focus when the `isEditing` state is `true`. Additionally, when the user finishes editing and clicks elsewhere causing the input field to lose focus, the `onBlur` event triggers, invoking the `modifyTodoTitle` function to update the title of the to-do item.