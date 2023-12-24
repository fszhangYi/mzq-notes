# Chakra-ui -- 比antd更具自由度

## 介绍
Chakra-ui是专门用于react开发的现代化框架。其官方地址为：`https://next.chakra-ui.com/docs/getting-started`

### 特点
- 内置Emotion库，天然支持css-in-js解决方案
- 基于Styled-Systems
- 支持开箱即用的主题功能
- 默认支持白天和黑夜两种主题样式
- 拥有大量功能丰富并且非常有用的组件
- 使得响应式的设计变得非常轻松
- 完整可用的文档
- 特别适合构建给用户展示的页面
- 框架活跃度高，目前还在积极维护中

### 下载
```shell
npm install @chakra-ui/core@1.0.0-next.x
npm isntall @chakra-ui/theme
```

这里需要强调的是，chakra的ui组件基于主题样式，所以必须首先引入主题。

## 基本使用
### 应用主题的框架和样式重置
```jsx
import { ChakraProvider } from '@chakra-ui/core';
import theme from '@chakra-ui/theme';

<ChakraProvider theme={theme}>
  <App />
</ChakraProvider>
```

一般来说还需要重置样式：
```jsx
import { ChakraProvider， CSSReset } from '@chakra-ui/core';
import theme from '@chakra-ui/theme';

<ChakraProvider theme={theme}>
  <CSSReset />
  <App />
</ChakraProvider>
```

### Style Props列表
chakra中的组件都支持使用props直接设置样式的行为，下表展示的是常用的一些style props:
![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c3805b4c017948a3a72a53f7ad66d578~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1184&h=602&s=95640&e=png&b=41444c)

使用举例：
```jsx
import {Box} from '@chakra-ui/core';
<Box w={200} h={200} bg='tomato' p='3px'> Hello chakra-ui </Box>
```

## 主题的使用
chakra提供了名为useColorMode的钩子函数，此钩子函数执行之后返回当前的主题和改变主题的方法。
```jsx
import { useColorMode } from '@chakra-ui/core';
const [colorMode, toggleColorMode] = useColorMode();

<Text>当前的颜色模式为{colorMode==='light'?'light':'dark'}</Text>
<Button onClick={toggleColorMode}>切换颜色模式</Button>
```

显然colorMode可以成为三目表达式的判断条件，以此条件判断可以定制在light或者dark模式下页面的显示状态的不同。

而同toggleColorMode方法执行的结果就是在light和dark模式两者之间进行切换。

**注意：chakra-ui对主题的切换是会同步到localStorage中的，也就是组件内部自动封装好了持久化；这意味着浏览器刷新之后当前的模式仍然是生效的**

上面使用三元表达式定制不同模式下的样式的做法显得不是很好，实际上组件提供了另外的钩子函数`useColorModeValue`，其中封装了对当前模式的判断：

```jsx
import { useColorModeValue } from '@chakra-ui/core';
...
const bgColor = useColorModeValue(lightValue, darkValue);
<Box bgColor={bgColor}></Box>
```

### 强制主题

如果使用了chakra中提供的封装组件，那么在mode切换的时候，在默认情况下，这些组件的样式也会相应的做出改变。有的时候可能会有如下的需求：
- 不论子啊light还是dark模式下都想要使用light下的主题配色，这个时候就可以使用chakra提供的`<lightMode>`组件了。被此组件包裹的内容不会再收到mode切换的影响，将会强制使用light模式下的配色。

```jsx
import {LightMode, DarkMode } from '@chakra-ui/core';

<LightMode>
    <Button onClick={toggleColorMode}>尝试切换主题颜色</Button>
</LightMode>
```

### 设置默认主题
chakra提供了两种设置默认主题的方法：'自定义'和'与操作系统风格保持一致'

#### 自定义默认主题
在根组件的return方法前面通过`theme.config.initialColorMode`进行设置，值为'light'|'dark'。
```jsx
import theme from "@chakra-ui/theme";
theme.config.initialColorMode = 'dark';
ReactDOM.render(
  <ChakraProvider theme={myTheme}>
    <CSSReset />
    <App />
  </ChakraProvider>,
  document.getElementById("root")
);

```

### 主题跟随操作系统
在根组件的return方法前面通过`theme.config.useSystemColorMode`进行设置，值为boolean类型的。
```jsx
import theme from "@chakra-ui/theme";
theme.config.useSystemColorMode = true;
ReactDOM.render(
  <ChakraProvider theme={myTheme}>
    <CSSReset />
    <App />
  </ChakraProvider>,
  document.getElementById("root")
);
```

## 主题对象
下面以主题中提供的颜色为例说明chakra中的主题对象。
### 1. Colors -- 定义颜色和背景色
![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/854653f07621484b9dcd972920d617c1~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=600&h=314&s=36951&e=png&b=fffefe)

使用方式：
`<Box color='gray.500'></Box>`

### 2. Space -- 定义布局间隔
使用space可以自定义项目之间的间距，这些间距可以供`padding margin top left right bottom`等属性使用
`<Box mb='5'></Box> // 这里的5表示的含义实际上是5*0.25rem的意思`

### 3. Sizes -- 定义尺寸大小
使用sizes对象可以自定义元素的大小，sizes可以由`width height max-width min-height`等属性使用。
`<Box x='lg'></Box> // 这里的lg表示的实际上是32rem的意思`

### 4. BreakPoints -- 定义媒体查询
breakPoints的作用是：配置响应数组中使用的默认断点，这些值将用于生成移动优先的媒体查询。

在theme.js中可以看到如下的代码：
```jsx
export default {
    breakPoints: ['30em', '48em', '62em', '80em'],
}
```
上面的代码所表示的含义其实是在宽度为'30em', '48em', '62em', '80em'时候进行的媒体查询。与此对应的使用方式为：

```jsx
<Box fontSize={["12px","14px","16px","18px","20px"]}></Box>
```
其中，12px表示的是不进行媒体查询的时候的默认值，而剩下的数值恰好与theme.js中的媒体查询一一对应。

## 自定义Chakra-ui组件
虽然chakra-ui中提供了较为丰富的组件，但是很多时候还需要开发者自己封装符合项目风格的自定义组件，这个时候就可以使用提供的`chakra`方法来实现封装的需求。

下面是一个自定义chakra组件的基本结构：
```jsx
// step 1
const options = {
    baseStyle:{},
    sizes:{},
    variants:{},
};
// step 2
const MyButton = chakra("button", options);
// step 3
MyButton.defaultProps = {};
// step 4
<MyButton>我的自定义按钮</MyButton>
```

下面完善此结构：
```jsx
// 导入React库，用于构建React组件
import React from 'react'; 
// 从Chakra UI库导入chakra函数，用于创建样式化组件
import { chakra } from '@chakra-ui/core'; 

// 使用chakra函数创建一个自定义的MyButton按钮组件
const MyButton = chakra('button', {
    baseStyle: { // 设置组件的基础样式
        borderRadius: 'lg', // 圆角设置为大(large)
    },
    sizes: { // 为不同的尺寸设定样式
        sm: {  
            px: '3',       // 设置水平内边距（左右内边距）
            py: '1',       // 设置垂直内边距（上下内边距）
            fontSize: '12px', // 设定字体大小为12px
        },
        md: {  
            px: '4',      // 设置水平内边距为4（左右内边距）
            py: '2',      // 设置垂直内边距为2（上下内边距）
            fontSize: '14px', // 设定字体大小为14px
        },
    },
    variants: { // 为按钮定义变体，根据variant属性的不同应用不同的样式
        primary: {
            bgColor: 'blue.500', // 主要变体的背景颜色为500级别的蓝色
            color: 'white',      // 字体颜色为白色
        },
        danger: {
            bgColor: 'red.500',  // 危险变体的背景颜色为500级别的红色
            color: 'white',      // 字体颜色为白色
        }
    },
});

// 设置MyButton的默认属性值
MyButton.defaultProps = {
    size: 'sm',     // 默认尺寸为小(sm)
    variant: 'primary', // 默认变体为主要(primary)
}

// 定义App组件
function App () {
    return (
        // 返回组件的JSX结构
        <div>
            {/* 使用MyButton并传递size和variant属性，分别设置为中等大小（md）和危险变体（danger） */}
            <MyButton size='md' variant='danger'></MyButton>
        </div>
    )
}

```

## 自定义全局Chakra-ui组件
上述自定义组件的方式是比较“浅”或者说“敏捷”的定制方式，但是项目中还存在着一部分需要全局通用的定制化组件，那么该如何做好这部分的基建呢？实际上，chakra-ui也提供了相应的自定义组件的挂载方式，使得开发者自定义的组件做到：**一次挂载，处处使用**

### 1. 工程化结构
```shell
mkdir -p src/chakra-ui
touch src/chakra-ui/index.js src/chakra-ui/button.js
```

### 2. 构建公共组件
```jsx
// button.js
const MyButton = {
  baseStyle: {},
  sizes: {},
  variants: {},
  defaultProps: {},
};

export default MyButton;
```
统一导入导出：
```jsx
import MyButton from './button';

export default {
    MyButton,
}
```

### 3. 在Provider的theme对象上挂载公共组件
```jsx
import MyComponents from '@/chakra-ui'; // @表示的是src目录的绝对路径

const myTheme = {
    ...theme,
    components: {
        ...theme.components,
        ...MyComponents,
    }
}

<ChakraProvider theme={myTheme}>{...}</ChakraProvider>
```

### 4. 在任意位置通过themeKey字段指定/使用自定义组件
```jsx
const MyButton = chakra('button', {
    themeKey: 'MyButton',
})

function App () {
    return (
        <div>
            <MyButton size='md' variant='danger'>我的定制按钮</MyButton>
        </div>
    )
}
```

## chakra-ui综合练习--制作一个登录表单
这个练习的目的是为了将上面的知识点串联起来，模拟实际项目开发过程中的场景。
### 1. 结构搭建
```shell
npx create-react-app chakra-ui-demo
cd chakra-ui-demo
rm -rf src/
mkdir -p src/assets/images
mkdir src/components
touch src/index.js
cd src/components
touch App.js Form.js Header.js Home.js Main.js SignIn.js SignUp.js
cd ../../
```

### 2. src/index.js入口文件中的内容
```
import React from "react";
import ReactDOM from "react-dom";
import { BrowserRouter as Router } from "react-router-dom";
import { ChakraProvider, CSSReset } from "@chakra-ui/core";
import App from "./components/App";
import theme from '@chakra-ui/theme';

ReactDOM.render(
  <Router>
    <ChakraProvider theme={theme}>
      <CSSReset />
      <App />
    </ChakraProvider>
  </Router>,
  document.getElementById("root")
);
```

### 3. src/components/App.js视图根组件中的内容
```jsx
import React from "react";
import Header from "./Header";
import Main from "./Main";

function App() {
  return (
    <>
      <Header />
      <Main />
    </>
  );
}

export default App;
```

### 4. src/components/Header.js页面框架布局组件中的内容
```jsx
// 导入React基础库
import React from "react";
// 从chakra-ui/core中导入所需的组件
import {
  Box,        // 用于布局的容器组件
  Stack,      // 用于水平或垂直排列子元素的组件
  Image,      // 用于展示图片的组件
  Button,     // 按钮组件
  useColorMode, // 用于获取和切换颜色模式的Hook函数
} from '@chakra-ui/core';
import { Link } from 'react-router-dom'; // 用于应用内导航的Link组件
import logo from "../assets/images/logo.png"; // 导入logo图片

// 定义并导出Header组件
export default function Header() {
  // 使用useColorMode Hook获取当前颜色模式及切换函数
  const { colorMode, toggleColorMode } = useColorMode();

  // 返回JSX结构
  return (
    // 包裹Header的最外层Box组件，设置高度和背景颜色
    // 根据当前色彩模式，动态设置背景颜色
    <Box
      h="60px"
      bg={colorMode === "light" ? "gray.200" : "gray.700"}>
      {/* 创建一个用于内容居中的容器Box */}
      <Box
        w="80%"     // 容器宽度为80%
        m="auto"    // 自动外边距实现水平居中
        overflow="hidden"> // 防止子元素溢出容器
        {/* Logo图片，浮动到左侧 */}
        <Image
          float="left"   // 图片左浮动
          mt="10px"      // 距离顶部间距
          w="40px"       // 图片宽度
          src={logo} />  // 图片资源路径
        {/* 网站导航栏组件Stack */}
        <Stack
          spacing={8}       // 子元素直接的空隙
          direction="horizontal" // 水平排列
          ml={8}            // 设定左外边距，与Logo保持一定间距
          h="60px"          // 栈的高度与外围Box保持一致
          float="left"      // 浮动居左，和Logo在一行
          align="center"    // 子元素垂直居中对齐
        >
          {/* 使用Link组件实现SPA的内部跳转 */}
          <Link to="/home">首页</Link>
          <Link to="/form">表单</Link>
          <Link to="/card">卡片</Link>
        </Stack>
        {/* 切换颜色模式的按钮 */}
        <Button
          colorScheme="teal"   // 使用teal主题颜色
          float="right"        // 按钮浮动到右侧
          mt="10px"            // 距离顶部间距
          onClick={toggleColorMode} // 点击时切换颜色模式
        >
          切换模式
        </Button>
      </Box>
    </Box>
  );
}
```

### 5. src/components/Main.js路由根组件中的内容
```jsx
import React from "react";
import { Route } from "react-router-dom";
import { Box } from "@chakra-ui/core";
import Form from "./Form";
import Card from "./Card";

export default function Main() {
  return (
    <Box w={1 / 2} mx="auto" mt="100px" d="flex" justifyContent="center">
      <Route path="/form" component={Form} />
      <Route path="/card" component={Card} />
    </Box>
  );
}

```

### 6. src/components/Card.js页面组件中的内容
```jsx
// 导入React基础库
import React from "react";
// 导入Chakra UI组件库中的组件
import { 
  Box,       // 用于布局的容器组件
  Image,     // 图片显示组件
  Badge,     // 标签组件，用于显示小徽章或者标记
  Text,      // 文本组件，显示各种文本内容
  Stack,     // 用于布局的组件，可水平或垂直堆叠子组件
  Flex,      // Flexbox布局组件，用于创建灵活的布局
  Button,    // 按钮组件
  useColorMode // Hook，用于获取当前的颜色模式以及切换颜色模式的函数
} from "@chakra-ui/core";
import chakraUI from "../assets/images/chakra-ui.png"; // 导入图像资源
import { AiFillStar } from "react-icons/ai"; // 导入星星图标，用于评级等

// 定义Card组件并导出
export default function Card() {
  // 使用useColorMode Hook获取当前颜色模式
  const {colorMode} = useColorMode();
  // 定义浅色和深色模式下背景色和文本颜色的配置
  let bgColor = {light: 'gray.200', dark: 'gray.700'};
  let textColor = {light: 'gray.500', dark: 'gray.100'};

  // 组件返回JSX结构
  return (
    // 外层容器Box用于整个卡片的布局
    <Box
      w={1 / 2}             // 卡片宽度为父容器的一半
      overflow="hidden"     // 溢出部分隐藏
      borderRadius="lg"     // 圆角
      boxShadow="lg"        // 阴影效果
      bgColor={bgColor[colorMode]} // 根据颜色模式设置背景颜色
    >
      {/* 图片组件Image显示chakra UI的logo */}
      <Image src={chakraUI} />
      {/* Box容器用于包裹内容部分，设置内边距 */}
      <Box p={3}>
        {/* 水平布局的Stack，用于布局徽章和描述 */}
        <Stack direction="horizontal" align="center">
          {/* 多个徽章Badge，显示各种标记，设置特定的颜色主题和四周圆角 */}
          <Badge variant="solid" colorScheme="teal" borderRadius="full" px={2}>
            NEW
          </Badge>
          <Badge variant="solid" colorScheme="teal" borderRadius="full" px={2}>
            Chakra-UI
          </Badge>
          <Badge variant="solid" colorScheme="teal" borderRadius="full" px={2}>
            React
          </Badge>
          {/* 文本组件Text，显示附加描述 */}
          <Text fontSize="sm" color={textColor[colorMode]}>
            周一去打工，一起呀
          </Text>
        </Stack>
        {/* 标题文本 */}
        <Text
          as="h2"               // 设置为h2的HTML元素
          color={textColor[colorMode]} // 根据颜色模式设置文本颜色
          fontWeight="semibold" // 设置字体粗细
          fontSize="xl"         // 设置字号大小
          mt="3"                // 设置上外边距
          mb="2"                // 设置下外边距
        >
          Chakra-UI 学习笔记
        </Text>
        <Text fontWeight="light" fontSize="sm" lineHeight="tall">
          { /* 文章内容描述 */ }
          Chakra UI 是一个简单的, 模块化的易于理解的 UI 组件库. 提供了丰富的构建
          React 应用所需的UI组件. 在整个应用程序中，在任何组件上快速、轻松地引用主题中的值。组件的构建考虑到了组合。你可以利用任何组件来创造新事物。Chakra-UI 严格遵循WAI-ARIA标准。所有组件都有适当的属性和现成的键盘交互。Chakra UI 是一个简单的, 模块化的易于理解的 UI 组件库. 提供了丰富的构建 React 应用所需的UI组件.
        </Text>
        {/* 星星评级和评论统计的Flex容器布局 */}
        <Flex py="2" align="center" fontSize="sm">
          {/* 星星图标，用AiFillStar组件表示，显示为teal颜色组 */}
          <Flex color="teal.500">
            <AiFillStar />
            <AiFillStar />
            <AiFillStar />
            <AiFillStar />
          </Flex>
          <AiFillStar />
          <Text ml={1}>300 评论</Text>
        </Flex>
      </Box>
      {/* 底部登录按钮，设置为占满整个卡片宽度 */}
      <Button colorScheme="teal" w="100%">登录</Button>
    </Box>
  );
}
```

### 7. src/components/Form.js表单组件中的内容
```jsx
// 引入React库
import React from "react";
// 引入Chakra UI库提供的多个组件
import {
  Box,         // 用于布局的盒子组件
  Image,       // 用于显示图片的组件
  Tabs,        // 用于创建标签页的容器组件
  TabList,     // 用于包裹Tab组件的列表容器
  Tab,         // 单个标签页的按钮组件
  TabPanels,   // 用于包裹TabPanel的容器
  TabPanel,    // 标签面板的组件，对应一个Tab
  useColorMode // 用于获取当前色彩模式和切换色彩模式的钩子（hook）
} from "@chakra-ui/core";

// 引入注册和登录组件
import SignUp from "./SignUp";
import SignIn from "./SignIn";

// 引入两种颜色模式下的Chakra UI相关的logo图片
import chakraLogoDark from "../assets/images/chakra-ui-dark.png"; // 深色模式logo
import chakraLogoLight from "../assets/images/chakra-ui-light.png"; // 浅色模式logo

// 定义Form组件并导出
export default function Form() {
  // 使用useColorMode钩子获取当前的颜色模式
  const { colorMode } = useColorMode();

  // 组件返回的JSX内容
  return (
    // 最外层的布局容器Box，设置背景色、内边距、阴影效果、圆角和宽度
    <Box
      bg={ // 根据colorMode动态设置背景色
        colorMode === "light" ? "gray.200" : "gray.700"
      }
      p={5} // 设置内边距
      boxShadow="md" // 设置中等强度的阴影
      borderRadius="lg" // 设置大圆角
      w="100%" // 设置宽度为100%
    >
      // Image组件用于显示logo图片
      <Image
        w={250} // 设置图片宽度
        mx="auto" // 设置水平外边距自动，以实现水平居中
        mb={6} // 设置下外边距
        mt={2} // 设置上外边距
        src={colorMode === "light" ? chakraLogoLight : chakraLogoDark} // 根据色彩模式动态设置图片来源
      />
      // Tabs组件用于创建标签页功能
      <Tabs isFitted> // isFitted属性意味这个标签页会填充整个容器宽度
        // TabList组件包裹多个Tab组件，作为标签页的标题列表
        <TabList>
          // Tab组件代表单个标签页的标题
          <Tab _focus={{ boxShadow: "none" }}>注册</Tab> // 使用_focus内联样式去掉焦点状态下的阴影
          <Tab _focus={{ boxShadow: "none" }}>登录</Tab>
        </TabList>
        // TabPanels组件包裹多个TabPanel组件，作为每个标签页面板的内容容器
        <TabPanels>
          // 每个TabPanel对应一个Tab，里面可以放置任何内容
          <TabPanel>
            <SignUp /> // 注册组件内容
          </TabPanel>
          <TabPanel>
            <SignIn /> // 登录组件内容
          </TabPanel>
        </TabPanels>
      </Tabs>
    </Box>
  );
}
```

#### 7.1 登录子组件中的内容
```jsx
// 引入React基础库
import React from "react";
// 引入Chakra UI中用于表单的组件
import {
  Input,              // 文本框组件，允许用户输入信息
  Stack,              // 堆叠布局组件，用于按序排列子组件
  InputGroup,         // 输入框组群组件，用于将几个表单元素组合在一起
  InputLeftAddon,     // 输入框左侧的前置标签组件
  InputRightAddon,    // 输入框右侧的后置标签组件
  FormControl,        // 用于表单控件的容器，提供有效性和禁用状态样式
  Button,             // 按钮组件，可用于提交表单或其他交互
  FormHelperText,     // 提供表单帮助或指示信息的文本组件
} from "@chakra-ui/core";

// 引入React Icons库中的图标
import { FaUserAlt, FaLock, FaCheck } from "react-icons/fa";

// 定义并导出SignIn组件
export default function SignIn() {
  // 组件返回的JSX内容
  return (
    <form>  // 用HTML的form标签包裹整个表单，这个标签在Web开发中用于收集用户输入
      <Stack spacing={5}>  // 使用Stack组件来垂直堆叠内部元素，spacing属性设置堆叠元素间的间距
        <FormControl isDisabled isInvalid>  // 使用FormControl组件包裹输入框，isDisabled禁用输入，isInvalid表示输入无效状态
          <InputGroup>  // InputGroup组件用来将输入框和前置/后置元素组织在一起
            <InputLeftAddon children={<FaUserAlt />} />  // InputLeftAddon作为前置标签，嵌入用户图标
            <Input placeholder="请输入用户名" />  // Input组件用于用户名输入，placeholder属性提供无内容时的提示文字
          </InputGroup>
          <FormHelperText>用户名是必填选项</FormHelperText>  // FormHelperText提供额外的帮助信息或提示
        </FormControl>
        // 第二个输入组：密码输入框，没有设置禁用或无效状态
        <InputGroup>
          <InputLeftAddon children={<FaLock />} />  //密码输入框左侧的锁图标标签
          <Input type="password" placeholder="请输入密码"/>  //设置type为password，隐藏密码输入；placeholder提供提示文字
          <InputRightAddon children={<FaCheck />} />  //密码输入框右侧的检查图标标签
        </InputGroup>
        // 登录按钮，点击可以提交表单
        <Button
          type="submit"   // 定义按钮为提交类型，可以触发表单提交
          boxShadow="xl"  // 设置按钮阴影为超大型尺寸
          w="100%"        // 宽度设置为100%，即撑满上级容器
          colorScheme="teal"  // 使用teal颜色主题
          _hover={{ bgColor: "tomato" }}  // 鼠标悬浮时背景变为番茄色
        >
          登录
        </Button>
      </Stack>
    </form>
  );
}
```
#### 7.2 注册子组件中的内容
```jsx
// 引入React库
import React from "react";
// 引入Chakra UI中用于表单的组件
import {
  Input,            // 文本框组件，允许用户输入信息
  Stack,            // 堆叠布局组件，用于按序排列子组件
  InputGroup,       // 输入框组组件，用于将多个表单元素组合起来
  InputLeftAddon,   // 输入框左侧添加组件，通常用于放置图标或文字等
  InputRightAddon,  // 输入框右侧添加组件
  FormControl,      // 表单控制组件，用于提供表单有效性和禁用状态样式
  Button,           // 按钮组件
  RadioGroup,       // 单选按钮组，管理多个Radio的选中状态
  Radio,            // 单选按钮组件
  Select,           // 下拉选择框组件
  Switch,           // 开关选择器组件
  FormLabel,        // 表单标签组件，用于定义表单字段的标签
  Flex,             // 弹性布局容器组件
  FormHelperText,   // 表单帮助文字，提供字段说明或帮助提示
} from "@chakra-ui/core";

// 引入react-icons图标库中的部分图标
import { FaUserAlt, FaLock, FaCheck } from "react-icons/fa";

// 定义并输出SignUp注册表单组件
export default function SignUp() {
  // 组件返回的JSX内容
  return (
    <form>  // 用form包裹整个注册表单
      <Stack spacing={5}>  // 使用Stack组件垂直堆叠子组件，spacing设置堆叠的间距
        <FormControl isDisabled isInvalid>  // 使用FormControl包装输入框，设置为禁用和无效显示
          <InputGroup>  // InputGroup组合输入框及左右添加的组件
            <InputLeftAddon children={<FaUserAlt />} />  // 左侧添加用户图标
            <Input placeholder="请输入用户名" />  // 输入框组件，placeholder提供输入提示
          </InputGroup>
          <FormHelperText>用户名是必填选项</FormHelperText>  // 提供额外的帮助信息或提示
        </FormControl>
        <InputGroup>  // 第二组输入框，密码输入
          <InputLeftAddon children={<FaLock />} />  // 左侧添加锁图标
          <Input type="password" placeholder="请输入密码"/>  // 输入框设置为密码类型，隐藏输入内容
          <InputRightAddon children={<FaCheck />} />  // 右侧添加检查图标
        </InputGroup>
        <RadioGroup defaultValue="0">  // 单选按钮组，默认选中第一个选项
          <Stack spacing={5} direction="horizontal">  // 水平排列单选按钮
            <Radio value="0">男</Radio>  // 单选按钮，值为"0"
            <Radio value="1">女</Radio>  // 单选按钮，值为"1"
          </Stack>
        </RadioGroup>
        <Select placeholder="请选择学科">  // 下拉选择框组件，设置选择提示
          <option value="React">React</option>  // 下拉选项，值为"React"
          <option value="Vue">Vue</option>      // 下拉选项，值为"Vue"
        </Select>
        <Flex>  // Flex容器用于布局开关选择器和标签
          <Switch id="deal" mr={2} />  // 开关选择器组件，id绑定到标签的htmlFor属性
          <FormLabel htmlFor="deal" mr="0" mb="0">  // 表单标签组件，指向id为"deal"的元素
            同意声明的协议
          </FormLabel>
        </Flex>
        <Button
          type="submit"  // 设置按钮为提交表单类型
          boxShadow="xl"  // 设置按钮阴影效果
          colorScheme="teal"  // 按钮使用teal色彩主题
          w="100%"  // 宽度设置为父容器的100%
          _hover={{ bgColor: "tomato" }}  // 鼠标悬浮时背景色变为"番茄"色
        >
          注册
        </Button>
      </Stack>
    </form>
  );
}
```