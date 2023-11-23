本文叙述了如何解决前端开发中经常遇到的一个棘手问题，即：如何修改antd组件内部元素的样式。

以Input组件为例，如果我想通过下面这种方式设置input组件的样式，是实现不了目的的！
```jsx
      <Input
        style={{
            background: `${error ? '#fff' : 'auto'}`,
            borderColor: `${error ? '#ff4d4f' : 'auto'}`,
        }}
        className={`${error ? 'input-status-error' : ''}`}
        {...props}
      ></Input>
```

原因也很简单，antd提供的Input组件本质上是span>input，所以style实际上设置到了wrapper组件也就是外面的span上面。

这就尴尬了，因为有时候确实是需要修改被包裹的input的样式的。

这个时候一般来说有两种做法：

做法一：使用styled-components

如下所示：

```jsx
import styled from 'styled-components';
const IInput = styled(Input)`
    input.input-status-error {
        background: #fff;
        borderColor: #ff4d4f;
    }
`;

<IInput
    className={`${error ? 'input-status-error' : ''}`}
    {...props}
></IInput>
```


做法二：使用less

如下所示：

```jsx
import styled from 'styled-components';
<Input
    className={`${error ? 'input-status-error' : ''}`}
    {...props}
></Input>
```

```less
input.input-status-error {
    background: #fff;
    borderColor: #ff4d4f;
}
```

这两个办法都有各自的缺点：

styled方法会动态生成className，这意味着你可能会遇到由于其动态修改className导致使用input输入文字的过程中，无缘无故的失去焦点，这对于交互是不可忍受的！

less方法最大的问题就是，它可能并不会生效！

好在antd升级之后提出了解决方案：

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/97ba14688bf94aa6a972034d6f0cdce2~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1161&h=89&s=6339&e=png&b=ffffff)

也就是说可以使用styles做结构性样式的设置（注意这里是styles而不是style！）

这样如果想要修改Input中包裹的input组件的样式，写成下面就可以了！

```jsx
      <Input
        styles={{
          input: {
            background: `${error ? '#fff' : 'auto'}`,
            borderColor: `${error ? '#ff4d4f' : 'auto'}`,
          },
        }}
        className={`${error ? 'input-status-error' : ''}`}
        {...props}
      ></Input>
```