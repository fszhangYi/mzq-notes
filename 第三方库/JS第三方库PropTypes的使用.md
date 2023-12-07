# JS第三方库PropTypes的使用

在前端应用的构建过程中，组件间的数据传递如同城池的暗道，信息在其中悄然流转。然而，信息的传递有时也会因不规范而产生错漏。此刻，PropTypes库就如守门人，确保穿流于组件间的数据类型和结构持之以恒，正确无误。在React项目中，使用PropTypes可谓稳若泰山，既保卫你的应用免受意外的入侵，又维护了代码的优雅与清晰。

## PropTypes基本应用

PropTypes允许定义组件属性的期望类型，预防未按预期接收属性的情况发生。基于不同数据类型与结构，以下是PropTypes的若干妙用。

### 1. 标准类型检查

```javascript
import PropTypes from 'prop-types';

function Greeting(props) {
  return <h1>Hello, {props.name}</h1>;
}

Greeting.propTypes = {
  name: PropTypes.string
};
```

上述代码为Greeting组件的name属性定义了字符串类型。PropTypes确保当此属性非字符串时能及时发现并提醒。

### 2. 必需属性检查

```javascript
Person.propTypes = {
  name: PropTypes.string.isRequired,
  age: PropTypes.number.isRequired
};
```

属性后接`.isRequired`修饰符表示该属性为必传。未传入必需属性，或类型不符，控制台会显示警告。

### 3. 数组和对象的元素类型指定

```javascript
Collection.propTypes = {
  items: PropTypes.arrayOf(PropTypes.number),
  details: PropTypes.objectOf(PropTypes.string)
};
```

`PropTypes.arrayOf`和`PropTypes.objectOf`指定数组和对象的元素类型。此处`items`是数字数组，`details`是字符串属性构成的对象。

### 4. 特定结构的对象检查

```javascript
User.propTypes = {
  info: PropTypes.shape({
    name: PropTypes.string,
    age: PropTypes.number,
    email: PropTypes.string
  })
};
```

通过`PropTypes.shape`指定对象的确切结构，如`info`对象包含有特定类型的`name`, `age`, 和`email`属性。

### 5. 单一类型或多种类型中的一个

```javascript
Component.propTypes = {
  value: PropTypes.oneOfType([
    PropTypes.string,
    PropTypes.number,
    PropTypes.instanceOf(Date)
  ])
};
```

`PropTypes.oneOfType`确保`value`属性是列举类型中的一种，增加了属性的灵活性。

### 6. 特定类的实例

```javascript
Threshold.propTypes = {
  comparer: PropTypes.instanceOf(ComparerClass)
};
```

`PropTypes.instanceOf`用来保证对应属性是特定类的实例，这里`comparer`必须是`ComparerClass`实例。

### 7. 任何可被渲染的内容

```javascript
MessageBox.propTypes = {
  message: PropTypes.node
};
```

`PropTypes.node`接受任何可被渲染的内容，包括数字、字符串、React元素等。

### 8. 枚举类型

```javascript
Alert.propTypes = {
  type: PropTypes.oneOf(['info', 'warning', 'error'])
};
```

`PropTypes.oneOf`定义枚举类型，控制属性的可能值在限定的范围内。

### 9. 自定义校验器

```javascript
Rating.propTypes = {
  stars: function(props, propName, componentName) {
    if (!/^[0-5]$/.test(props[propName])) {
      return new Error(`Invalid prop \`${propName}\` supplied to \`${componentName}\`. Validation failed.`);
    }
  }
};
```

如果内建的校验器不满足需求，可以定义函数形式的校验器，如`stars`属性需要是0到5的数字。

### 10.propTypes 默认值

```javascript
Component.defaultProps = {
  name: 'John Doe'
};

Component.propTypes = {
  name: PropTypes.string
};
```

通过`Component.defaultProps`设置属性的默认值，此时即使外部未传入`name`属性，组件亦可获得默认值。

## 进阶PropTypes使用

在进行React应用开发时，组件往往承载着复杂多变的属性，PropTypes的进阶使用正能够妙手搭建更加坚实的高楼大厦。

### 11.定制嵌套形状的对象

```javascript
Document.propTypes = {
  file: PropTypes.shape({
    name: PropTypes.string,
    property: PropTypes.shape({
      type: PropTypes.oneOf(['PDF', 'DOC']),
      size: PropTypes.number.isRequired
    }).isRequired
  })
};
```

嵌套使用`PropTypes.shape`可对对象内更深层的结构进行校验。上例中`file`属性具有`name`和`property`两个属性。

### 12. 数组中包含特定结构的对象

```javascript
Library.propTypes = {
  books: PropTypes.arrayOf(PropTypes.shape({
    id: PropTypes.number.isRequired,
    title: PropTypes.string,
    author: PropTypes.string
  }))
};
```

`PropTypes.arrayOf`配合`PropTypes.shape`适用于数组中的每个对象遵循特定的结构。

### 13. 允许某些特定值为null

```javascript
Profile.propTypes = {
  bio: PropTypes.string,
  age: PropTypes.number,
  school: PropTypes.oneOfType([
    PropTypes.string,
    PropTypes.oneOf([null])
  ])
};
```

通过结合`PropTypes.oneOfType`和`PropTypes.oneOf`，允许`school`属性为空。

### 14. 自定义数组或对象类型的结构

```javascript
Palette.propTypes = {
  colors: PropTypes.arrayOf(function(propValue, key, componentName, location, propFullName) {
    if (!/^#[0-9A-F]{6}$/i.test(propValue[key])) {
      return new Error(
        `Invalid prop \`${propFullName}\` supplied to \`${componentName}\`. ` +
        `Invalid color format. Validation failed.`
      );
    }
  })
};
```

对于复杂的结构，如需要校验的颜色码，可自定义方法校验数组中的每个值。
