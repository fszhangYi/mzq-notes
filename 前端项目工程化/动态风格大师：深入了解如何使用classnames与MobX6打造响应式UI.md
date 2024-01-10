`classnames` 是一个常用的 JavaScript 第三方库，它用于方便地操作 DOM 的 class 字符串。在构建动态的、响应式的、以及具有复杂状态的 web 应用时，`classnames` 库提供了简单的 API 来条件性地组合不同的 class 名称。

- **安装** 📦

  在使用 `classnames` 前，需要先将其安装到你的项目中。可以使用 `npm` 或 `yarn` 来完成安装：
  
  ```bash
  npm install classnames
  ```
  
  或者
  
  ```bash
  yarn add classnames
  ```

- **基本用法** 🔧

  一旦安装，就可以在项目中引入并使用它：
  
  ```javascript
  import classNames from 'classnames';
  
  const buttonClass = classNames('btn', 'btn-primary', 'large');
  // 最终 buttonClass 的值为 'btn btn-primary large'
  ```

- **条件性添加类名** ⚖️

  `classnames` 的一个主要功能是可以根据条件动态地添加或忽略类名。例如：
  
  ```javascript
  import classNames from 'classnames';
  
  const Button = ({ isActive, isDisabled }) => {
    const buttonClass = classNames(
      'btn',
      { 'btn-active': isActive, 'btn-disabled': isDisabled }
    );
  
    return <button className={buttonClass}>Click me</button>;
  };
  ```

- **与其他数据结构相结合** 🧩

  `classnames` 也支持数组和对象以外的其他数据类型，如下所示：
  
  ```javascript
  import classNames from 'classnames';
  
  const classes = classNames({
    'text-success': true,
    'text-error': false
  }, 'static-class', ['responsive-class']);
  
  // 最终 classes 的值为 'text-success static-class responsive-class'
  ```

- **与 React 一起使用** ⚛️

  在 React 项目中，很容易和组件的 `className` 属性配合使用：
  
  ```javascript
  import React from 'react';
  import classNames from 'classnames';
  
  const MyComponent = ({ additionalClass, isImportant }) => (
    <div className={classNames('my-component', additionalClass, { 'my-component-important': isImportant })}>
      This is a component content
    </div>
  );
  
  export default MyComponent;
  ```

- **使用classnames和mobx赋能前端样式交互** 🔄

  使用 `classnames` 与 `MobX` 结合通常是在已经有一个由 `MobX` 管理的状态并且需要根据这个状态来动态改变类名的场景。以下是 `classnames` 和 `MobX 6` 结合使用的一个简单示例：
  
  ```bash
  npm install mobx mobx-react classnames
  ```

  ```javascript
  import { makeAutoObservable } from 'mobx';

  class ToggleStore {
    buttonActive = false;

    constructor() {
      makeAutoObservable(this);
    }

    toggleButtonActive() {
      this.buttonActive = !this.buttonActive;
    }
  }

  const toggleStore = new ToggleStore();
  export default toggleStore;

  import React from 'react';
  import { observer } from 'mobx-react';
  import classNames from 'classnames';
  import toggleStore from './ToggleStore';

  const Button = observer(() => {
    const buttonClass = classNames('btn', {
      'btn-active': toggleStore.buttonActive
    });

    const handleButtonClick = () => {
      toggleStore.toggleButtonActive();
    };

    return (
      <button className={buttonClass} onClick={handleButtonClick}>
        {toggleStore.buttonActive ? 'Active' : 'Inactive'}
      </button>
    );
  });

  export default Button;
  ```

- **结论** ✅

  `classnames` 是一个轻量级而有力的工具，它适用于动态构建 class 名称。适合在任何需要操作 class 名称的 JavaScript 或 React 项目中使用。

---

### English version

- **Installation** 📦

  Before using `classnames`, you need to install it into your project using either `npm` or `yarn`:
  
  ```bash
  npm install classnames
  ```
  
  Or
  
  ```bash
  yarn add classnames
  ```

- **Basic Usage** 🔧

  Once installed, you can import and use it in your project:
  
  ```javascript
  import classNames from 'classnames';
  
  const buttonClass = classNames('btn', 'btn-primary', 'large');
  // The final value of buttonClass will be 'btn btn-primary large'
  ```

- **Conditional Class Names** ⚖️

  A major feature of `classnames` is its ability to dynamically add or ignore class names based on conditions. For example:
  
  ```javascript
  import classNames from 'classnames';
  
  const Button = ({ isActive, isDisabled }) => {
    const buttonClass = classNames(
      'btn',
      { 'btn-active': isActive, 'btn-disabled': isDisabled }
    );
  
    return <button className={buttonClass}>Click me</button>;
  };
  ```

- **Combining with Other Data Structures** 🧩

  `classnames` also supports data types other than arrays and objects, as shown below:
  
  ```javascript
  import classNames from 'classnames';
  
  const classes = classNames({
    'text-success': true,
    'text-error': false
  }, 'static-class', ['responsive-class']);
  
  // The final value of classes will be 'text-success static-class responsive-class'
  ```

- **Use with React** ⚛️

  In React projects, it is easy to combine with the `className` property of components:
  
  ```javascript
  import React from 'react';
  import classNames from 'classnames';
  
  const MyComponent = ({ additionalClass, isImportant }) => (
    <div className={classNames('my-component', additionalClass, { 'my-component-important': isImportant })}>
      This is a component content
    </div>
  );
  
  export default MyComponent;
  ```

- **Combining `classnames` with MobX for Front-End Style Interaction** 🔄

  Combining `classnames` with `MobX` is common in scenarios where you already have a state managed by `MobX` and need to dynamically change class names based on this state. Here is a simple example of using `classnames` with `MobX 6`:
  
  ```bash
  npm install mobx mobx-react classnames
  ```

  ```javascript
  import { makeAutoObservable } from 'mobx';

  class ToggleStore {
    buttonActive = false;

    constructor() {
      makeAutoObservable(this);
    }

    toggleButtonActive() {
      this.buttonActive = !this.buttonActive;
    }
  }

  const toggleStore = new ToggleStore();
  export default toggleStore;

  import React from 'react';
  import { observer } from 'mobx-react';
  import classNames from 'classnames';
  import toggleStore from './ToggleStore';

  const Button = observer(() => {
    const buttonClass = classNames('btn', {
      'btn-active': toggleStore.buttonActive
    });

    const handleButtonClick = () => {
      toggleStore.toggleButtonActive();
    };

    return (
      <button className={buttonClass} onClick={handleButtonClick}>
        {toggleStore.buttonActive ? 'Active' : 'Inactive'}
      </button>
    );
  });

  export default Button;
  ```

- **Conclusion** ✅

  `classnames` is a lightweight yet powerful tool suitable for dynamically constructing class names. It offers a clear and concise syntax, enhancing the development experience and the neatness of the outcome, making it a great choice for any JavaScript or React project involving class name manipulation.

