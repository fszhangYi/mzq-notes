# React Native

## 概述

五大方面内容：

*   简介
*   搭建开发环境
*   基础语法
*   架构原理
*   项目实践

## 1. 简介

FaceBook 2015 IOS Android <https://reactnative.dev/>

移动App的开发模式：
原生开发->原生App->Android、iOS、Windows（X）
混合开发->混合App->Reactive Native（FaceBook）、Weex（Alibaba）、Flutter（Google）
H5开发->Web App->HTML、CSS、JavaScript
引擎 JSCore、V8、Flutter engine

### RN的优势

*   开发体验好
*   开发成本低
*   学习成本低

### RN和客户端的关系

RN-->Bridge-->Andriod(Kotlin | Java)
\|----->IOS(Swift | Objective-C)

### RN的劣势

*   不成熟
*   性能差
*   兼容性差

### 搭建开发环境概述

*   1.  基础环境
*   2.  搭建安卓环境
*   3.  搭建iOS环境
*   4.  初始化项目

1.  基础环境搭建
    安装Node.js->安装Yarn->安装RN脚手架(npm install -g react-native-cli)

!windows下只能搭建Android开发环境；但是Mac下既能搭建Android开发环境，也能搭建iOS开发环境

搭建环境需要科学上网

### 搭建安卓开发环境

*   1.  安装JDK
*   2.  安装Android Studio
*   3.  安装Android SDK
*   4.  配置环境变量

1.  安装SDK：必须是1.8版本的，需要登陆，然后一直下一步即可，使用java -version验证
2.  安装Android Studio：下载、安装、启动Android Studio，然后创建项目

！Android Studio会自动安装最新版本的SDK，但是RN需要特定版本的SDK，这就是为什么需要单独下载SDK的原因了。
在Tools下找到SDK Manager，然后按照下面的顺序进行：

![步骤1.PNG](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0ee7cb30c11e4f61998b027f57c64959~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=967&h=698&s=327038&e=png&b=f5f4f4)

![步骤2.PNG](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c120421ed4864bb5acda4d5b32480713~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=985&h=711&s=285882&e=png&b=f6f5f5)


![步骤3.PNG](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/901318ba5b474e849af40bc414a24ff8~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=771&h=763&s=231787&e=png&b=f8f7f7)


![步骤4.PNG](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5860e28e2cce4c7fb76dafbea8765c40~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=774&h=878&s=186870&e=png&b=40444d)


![步骤5.PNG](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/794f556675144fc5a903c2645ca52e5f~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=781&h=440&s=171733&e=png&b=f2f1f1)
### 初始化项目

1.  创建项目 `react-native init myproject && cd myproject && yarn android`
2.  安装vscode插件 **ES7 React/Redux/GraphQL/React-Native snippets** 使用快捷命令，例如：rnc rnf
3.  调试工具: 模拟器会随着Android Studio和Xcode一起安装，启动应用，模拟器也会一起启动-在模拟器中点击一下，然后按下ctrl+m选择debug开始调试
4.  真机调试：打开USB调试模式-通过USB将电脑和手机连接起来-启动应用，在手机上安装应用

### 推荐的开发环境

*   win10
*   vs code
*   rn\@0.63.3
*   react\@16.13.1

### 基础语法 - style

*   1.  掌握React：JSX 组件 生命周期 Hook API Redux 常用安装包
*   2.  Style Sheet
*   3.  Flexbox
*   4.  组件和API
*   5.  路由与导航

RN中的样式与CSS中是不同的：没有继承性（只有Text组件有继承性）、样式名采用小驼峰、所有尺寸都没有单位、有些特殊的样式名称在web开发的时候是没有的（marginHorizontal marginVertical）

样式声明的两种方式：对象或者数组对象（如果是数组形式的，后面的可以覆盖前面的）

样式的使用方式：

```js
import { StyleSheet } form 'react-native';
const styles = StyleSheet.create({
    foo: fooStyleObj,
    bar: barStyleObj,
});

// 使用
style = {[styles.foo, styles.bar]}
```

### 基础语法 - FlexBox

*   1.  基本概念：容器、项目、主轴（默认为竖直方向）、交叉轴
*   2.  常见属性：flexDirection、justifyContnent、alignItems、flex
*   3.  获取屏幕尺寸：`import {Dimensions} from 'react-native'; const windowSize = Dimensions.get('window'); const windowWidth = windowSize.width;`

### 基础语法 - 组件和API

*   1.  简介
*   2.  核心组件：本质上是对原生组件的封装，是来自react-native库的组件；例如Image组件实际上是对Android中的ImageView或者iOS中的UIImageView组件的封装；View或者Text组件也是如此。核心组件可以分成以下几类：基础组件、交互控件、列表视图、iOS或者Android独有组件、其他组件。具体来说核心组件有：View Text Alert Button Switch StatusBar ActivityIndicator Image TextInput Touchable ScrollView SectionList FlatList Animated（组件必须经过特殊处理才能够有动画）
*   3.  第三方组件
*   4.  自定义组件

### Image组件

*   作用： 加载图片
*   加载方式：本地路径、网络路径、Base64格式

### Touchable组件

*   TouchableHighlight：触碰后高亮显示
*   TouchableOpacity：触碰之后透明度降低
*   TouchableWithoutFeedback：触碰之后没有响应

### SafeAreaView组件

用来解决布局中的内容跑到刘海屏下面的问题的组件。

### 可以直接使用动画的组件

*   Animated.View
*   Animated.Text
*   Animated.ScrollView
*   Animated.Image

创建动画的步骤：

1.  Animated.Value() 初始化单个值
2.  Animated.ValueXY() 初始化向量值
3.  将初始值绑定到动画组件上去，例如：opacity translate
4.  逐帧修改初始值：

*   Animated.decay
*   Animated.spring
*   Animated.timing

### 第三方组件

*   指的是需要单独通过第三方库安装的组件
*   使用步骤：安装-配置-使用
*   常见的第三方组件：WebView Picker Swiper AsyncStorage Geolocation Camera

### WebView

*   安装：yarn add react-native-webview
*   配置：<https://github.com/react-native-webview/react-native-webview>
*   使用：指定uri或者直接渲染html

### Picker

*   安装：yarn add @react-native-picker/picker
*   配置：<https://github.com/react-native-picker/picker>
*   使用：注意Android和iOS的区别

### Swiper

*   安装：yarn add @react-native-swiper
*   配置：<https://github.com/leecade/react-native-swiper>

### AsyncStorage

*   安装：yarn add @react-native-async-storage/async-storage
*   配置：<https://github.com/react-native-async-storage/async-storage>
*   使用：用来做增删改查用的；一般会对其进行封装并提供set\delete\update\get方法

### Geolocation

*   安装：yarn add @react-native-community/geolocation
*   配置：<https://github.com/react-native-geolocation/react-native-geolocation;然后添加获取位置的授权>
*   使用：通过手机获取经纬度信息

### Camera

*   安装：yarn add react-native-camera
*   配置：<https://github.com/react-native-camera/react-native-camera;然后添加获取摄像头的授权>
*   使用：拍照、扫码、人脸识别

### Image-Picker

*   安装：yarn add react-native-image-picker
*   配置：<https://github.com/react-native-image-picker/react-native-image-picker;然后添加获取摄像头的授权>
*   使用：拍照、扫码、人脸识别、相册

### 自定义组件

*   指的就是开发自己封装的可复用的组件

## 路由和导航

*   1.  简介
*   2.  基础组件
*   3.  Stack导航
*   4.  BottomTab导航
*   5.  Drawer导航
*   6.  MaterialTopTab导航

不同于web开发中使用的是React-Router实现路由导航，在RN中是通过React-Navigation进行导航的

5.x <https://reactnavigation.org/>

路由的使用：

![路由.PNG](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cdd69ace40b44f83a0ada657cb4fa127~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1519&h=593&s=106284&e=png&b=3f434c)


![路由2.PNG](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f4ed9f52e0284c4b99a3f327dd264c86~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1572&h=638&s=127165&e=png&b=3f434c)
```js
import 'react-native-gesture-handler';
import {NavigationContainer} from '@react-navigation/native';

export const App = () => {
    return (
        <NavigationContainer>...</NavigationContainer>
    )
}
```

### Stack导航

*   RN中是没有web端的history对象的，在使用路由之前需要将组件先声明在Stack中：
*   yarn add @react-navigation/stack

使用举例：

```js
import {NavigationContainer} from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack'
const Stack = createStackNavigator(); 

const HomeScreen = ({}) => {
    return (
        <Button onPress={()=>void navigation.navigate('Details')} title="跳转到详情页">
    )
}

export const App = () => {
    return (
        <NavigationContainer>
            <Stack.Navigator initialRouteName="Details">
                <Stack.Screen name="Home" component={HomeScreen}>
            </Stack.Navigator>
        </NavigationContainer>
    )
}
```

可以看出来，重要的是：Stack.Navigator Stack.Screen和navigation.navigate.

Navigator属性：

*   initialRouteName
*   headerMode: float screen none
*   screenOptions

Screen属性：

*   options
    *   title
    *   headerTitleStyle
    *   headerStyle
    *   headerLeft
    *   headerRight
    *   headerTinyColor

详细举例如下所示：

![详情.PNG](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c2883809b4cd4992b9694e674ca16fc1~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1038&h=850&s=221901&e=png&b=282c34)

### BottomTab导航
- 安装：yarn add @react-navigation/bottom-tabs
- 使用：
```js
import { createBottomTabNavigator } from 'react-navigation/bottom-tabs';

const Tab = createBottomTabNavigator();

export default function App () {
    return (
        <NavigationContainer>
            <Tab.Navigator>
                <Tab.Screen name="Home" component={HomeScreen} />
            </Tab.Navigator>
        </NavigationContainer>
    )
}
```

为BottomTab导航菜单添加图标的过程：

使用的是名为`React-native-vector-icons`的组件库:
- 安装：npm install --save react-natice-vector-icons
- 链接：将组件库链接到应用中去：https://github.com/oblador/react-native-vector-icons
- 使用：具体的使用办法可以到官网上查看


### Drawer导航
- 安装：npm install @react-navigation/drawer
- 使用：
```js
import { createDrawerNavigator } from '@react-navigation/drawer';

const Drawer = createDrawerNavigator();

// 然后使用Drawer.Navigator和Drawer.Screen
```

Drawer导航相关属性：
- drawerPosition: right | left 表示的是导航来的位置
- drawerType: front | back | slide | permanent 表示的是菜单动画效果
- drawerStyle: backgroundColor width 表示的是菜单样式
- drawerContentOptions: activeTinyColor itemStyle 表示的是选中菜单的时候的样式

Screen相关的属性：
- options: title drawerLabel(可以看成用组件代替了title) drawerIcon(函数，用来返回图标) headerShown(是否显示header) headerLeft和headerRight(用来声明header左侧和右侧的内容)


### MaterialTopTab导航
- 安装：yanr add @react-navigation/material-top-tabs react-native-tab-view
- 使用：
```js
import {createMaterialTopTabNavigator} from '@react-navagation/material-top-tabs';

const Tab = createMaterialTopTabNavigator();

// 然后声明Tab.Navigator 和 Tab.Screen
```

MaterialTopTab导航相关的属性
- Navigator属性：
    - tabBarPosition: top | bottom 表示的是Tab显示的位置
    - tabBarOptions: activeTintColor | inactiveTintColor | showIcon | showLabel | tabStyle | labelStyle(高优先级) | iconStyle
- Scree属性：
- options属性：
    - title: 标题
    - tabBarIcon: 设置标签图标-focused和color
    - tabBarLabel: 设置标签的文字内容-focused和color


### 路由嵌套
- 作用：在一个导航的内容渲染另一个导航
- 举例：
```js
Stack.Navigator
 Home(Tab.Navigator)
  Feed(Screen)
  Messages(Screen)
  Settings(Screen)
```

### 路由传参
- 方式：第二参数--navigation.navigate('路由地址', {key:1})
- 接受：被跳转到的组件由于Provider的包裹，因此可以再props中接收到路由参数，对于类组件和函数式组件：
    - this.props.route.params.key
    - route.params.key


## RN架构原理
- 1. 现有架构
- 2. 新的架构

### 现有架构的原理
[现有架构](./架构.png)
[现有架构设计](./架构设计.png)

### 线程模型
- Js线程： Metro 执行js的线程
- Main线程： UI或者原生线程
- Shadow线程： Shadow Tree和虚拟BOM Layout线程 Yoga引擎翻译Flexbox布局(在原生端是不支持css中的Flex布局的)

#### 渲染机制
[三者关系](./渲染原理.png)
#### 数据交互
[数据交互](./渲染原理.png)
#### 启动过程
[启动过程](./启动过程.png)
#### 应用过程
[应用过程](./应用过程.png)


### 架构原理
- 重构原因
    - 之前的架构上存在许多性能问题
    - Flutter等后起之秀的压力
    - 2018.06-2020.12

- 修改内容
    - React 16+
    - CodeGen 静态检查（是FaceBook推出的代码生成工具），旨在减少类型错误（可对ts或者flow代码进行转换）和通信次数
    - JSI 允许不同的js解析引擎，还可以直接和Native进行通信，旨在减少通信压力，如避免序列和反序列化
    - Fabric和TurboModules两部分，Fabric可以看成是新的UI层，并且简化了之前的渲染流程；而TurboModules一方面配合JSI直接调用原生模块，一方面实现了模块的按需加载
    - Native中的很多模块分发至社区进行维护--例如：AsyncStorage WebView; 精简之后的称之为**Lean Core**
[改进架构](./改进架构.png)

## 项目实践
1. 项目展示
2. 数据接口
3. UI界面
4. 状态管理
5. 项目优化


### 数据接口
- 申请接口：后端、Mock、第三方接口（和风天气）
- 调试接口： 使用轻量级的insomnia (https://insomnia.rest/) 项目配置变量
- 使用接口

### UI界面
1. 界面
2. 新闻页
3. 用户页

### 背景渐变色 使用`react-native-linear-gradient`
- 安装： yarn add react-native-linear-gradient
- 配置： https://github.com/react-native-linear-gradient/react-native-linear-gradient
- 使用：
```js
import LinearGradient from 'react-native-linear-gradient';

<LinearGradient start={{x:0,y:0}} end={{x:1,y:0}} colors={['#ddd','#333']}>...</LinearGradient>
```

### 用户页
- 个人中心
- 登录页
- 注册页

### 动画 `react-native-animatable`
- 安装：yarn add react-native-animatable
- 使用：
```jsx
import * as Animate from 'react-native-animatable';
<Animatable.View animation='fadeInUpBig'>
```

### paper `react-native-paper`
- 安装：yarn add react-native-paper
- 配置：
- 使用：

### 状态管理
- Redux
- 路由鉴权

### 项目优化
- 添加项目的logo
- 修改项目的名称
