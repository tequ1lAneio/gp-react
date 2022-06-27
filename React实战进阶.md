# React实战进阶

## React基础

### 特性简介

传统：局部刷新

react：整体刷新

#### Flux架构

单向数据流，追求以状态为基础来展示UI。

![Flux架构](/Users/57block/lgp/gp-react/note-images/Flux架构.jpg)

Flux架构的衍生项目：**Redux**和**MobX**

### 理解React组件

- React组件一半不提供方法，而是某种状态机
- React组件可以理解为一个纯函数
- 单向数据绑定

#### 受控组件和非受控组件

- 受控组件：元素状态由使用者维护
- 费受控组件：元素状态DOM自身维护

#### 何时创建组件：单一职责原则

- 每个组件只做一件事
- 如果组件变得复杂，那么应该拆分成小组件

#### JSX的本质：动态创建组件的语法糖

```jsx
const name = 'Joe'
React.createElement('h1', null 'Hello, ', name);
```

- JSX本身也是表达式

- 在属性中使用表达式

- 延展属性

  ```jsx
  const greeting = <Greeting {...props} />
  ```

- 表达式作为子元素

#### JSX优点

- 声明式创建界面的直观
- 代码动态创建界面的灵活
- 无需学习新的模板语言

#### React生命周期

![React生命周期](/Users/57block/lgp/gp-react/note-images/react-lifecycle.jpg)

##### constructor

- 用于初始化内部状态，很少使用
- 唯一可以直接修改state的地方

##### getDerivedStateFromProps(16.3后特性)

- 当state需要从props初始化时使用
- 尽量不要使用：维护两者状态一致性会增加复杂度
- 每次render都会调用
- 典型场景：表单控件获取默认值

##### componenDidMount

- UI渲染完成后调用
- 只执行一次
- 典型场景：获取外部资源

##### componentWillUnmount

- 组件移除时调用
- 典型场景：资源释放

##### getSnapshotBeforeUpdate

- 在页面render之前调用，state已更新
- 典型场景：获取render之前的DOM状态

##### componentDidUpdate

- 每次UI更新时被调用
- 典型场景：页面需要根据props变化重新获取数据

##### shouldComponentUpdate

- 决定Virtual DOM是否要重绘
- 一般可以由PureComponent自动实现
- 典型场景：性能优化

### Virtual DOM

**JSX的运行基础：Virtual DOM**

React组件在内部维护了一套虚拟DOM的状态，这套DOM状态最终会映射到真正的DOM节点上。当虚拟DOM的状态发生变化的时候，内部需要计算虚拟DOM之间的区别，产生一个diff，最终并不是在真实DOM节点上整体刷新DOM，而是把diff的部分用高效的方式更新到UI上，从而保证性能。

标准的树的diff复杂度是On3，如果直接对比是开销很大的。

React采用的方式：**广度优先分层比较**

- 如果两个节点完全一致，直接比较下一层

- 如果发现两个节点顺序不用，直接调整位置

- 如果发现节点不同，则直接删掉节点，插入新的节点。此时react不关心该旧节点是否已经在其他地方被使用。

- **节点的跨层移动（react非常核心的优化点）**

  ![节点的跨层移动](/Users/57block/lgp/gp-react/note-images/节点跨层移动.jpg)

  react注意到B下面的节点不在了，于是直接删掉B已经B下面的节点。到下一层时，react并不关心D节点是哪来的或者有没有其他地方在用，会直接创建一个新的D节点。

**React使用DOM的前提是建立在两个假设之上：**

- 组件的DOM解构是相对稳定的
- 类型相同的兄弟节点点可以被唯一表示（所以同层的相同类型元素尽量加上key属性）

### 组件设计模式

组件复用的另外两种形式：**高阶组件**和**函数作为子组件**

#### 高阶组件

对已经组件进行封装后，形成一个新的组件。在高阶组件中包含自己的应用逻辑，这些逻辑会产生新的状态，然后这些状态会被传到已有的组件。所以高阶组件一般不会有自己的UI展现，而只是负责为它封装的组件提供一些额外的功能或者数据。

#### 函数作为子组件

函数作为子组件是一种编写react代码的设计模式，而不是react功能。

```react
class MyComponent extends React.Component {
  render() {
    return (
    	<div>
      	{this.props.children('Nate Wang')}
      </div>
    )
  }
}

<MyComponent>
	{(name) => (
    <div>{name}</div>
  )}
</MyComponent>
```

### Context API

Context提供了一个无需为每层组件手动添加props，就能在组件树间进行数据传递的方法。

```react
const ThemeContext = React.createContext('light')

class App extends React.Component {
  render() {
    return (
    	<ThemeContext.Provider value="dark">
      	<ThemedButton />
      </ThemeContext.Provider>
    )
  }
}
 
function ThemedButton(props) {
  return (
  	<ThemeContext.Consumer>
    	{theme => <Button {...props} theme={theme} />}
    </ThemeContext.Consumer>
  )
}
```

如果Consumer没有作为Provider的子元素出现，Consumer只能取到Context的默认值。

### React脚手架

#### create-react-app

facebook官方脚手架，内部包含Babel，Webpack配置，Jest，ESLint等

#### Rekit

除了create-react-app中包含的生态功能外，整合了更多其他插件，比如React Router，Less/Sass等，同时提供了大型项目结构的最佳实践，帮助把大项目的复杂度拆封成一个个的小的feature，每个小feature尽量保持独立，feature之间尽量松耦合。

#### Codesandbox.io

使用在线IDE创建项目和编辑代码，除了react，支持各个主流框架。

### 打包和部署

为什么需要打包？

- 编译新的ES语法，编译JSX
- 整合资源，例如图片，Less/Sass，SVG等
- 优化代码体积

#### webpack

把项目中所有的资源进行整合，对于每一种资源，webpack可以通过插件的方式载入对应的loader，比如react-loader、sass-loader、url-loader，loader决定可以支持什么类型的语法。最后webpack输出的是可以被部署的资源。

使用webpack这类软件的优点是基本不用关心如何去打包，只要写好自己的业务代码即可。

#### 打包的注意事项

- 设置nodejs环境为production（以react为例，开发环境下，react会去检查所有传入的属性类型是不是正确，对一些低级错误会给出warning提醒，而在生产环境下不会。）
- 禁用开发时的专用代码，比如logger（react-logger等）
- 设置应用根路径（比如package.json中的homePage）

## React生态

### Redux

Redux的存在，相当于在组件外部存放一个唯一的store，负责管理所有的全局的状态。

