# 《React设计模式与最佳实践》

## 第1章 React 基础
介绍了声明式编程，和react怎么符合声明式。

## 第2章 整理代码
介绍了jsx的语法，<br>
一个好用的babel插件jsx-control-statements，用于在jsx中写控制语句。<br>
还有一些eslint、函数式相关的内容。

## 第3章 开发真正可复用的组件
比较了以下两种方法：
1. createClass 工厂方法
2. 继承React.Component

比较的内容有：
* prop的校验
* prop的默认值
* state的初始化

同时提出来了一些最佳实践：
* 传递给组件的方法，应该在constructor里面先用bind绑定一下上下文
* 总是将setState 方法当作异步的（老实说，在这一点上我觉得react比vue要来得难用多了）
* react-docgen用于文档生成
* react-storybook-addon生成风格指南

## 第4章 组合一切
容器组件与表现组件模式:
* 容器组件：
    1. 更关心行为部分；
    2. 负责渲染对应的表现组件；
    3. 发起API 请求并操作数据；
    4. 定义事件处理器；
    5. 写作类的形式。
* 表现组件：
    1. 更关心视觉表现；
    2. 负责渲染HTML 标记（或其他组件）；
    3. 以props 的形式从父组件接收数据；
    4. 通常写作无状态函数式组件。

mixin与高阶组件：
* mixin：
    1. 允许合并生命周期方法和初始状态。
    2. 代码会对mixin产生依赖
    3. 常见问题是冲突
* 高阶组件：<br>
相当于新定义一个组件，这个组件包裹着旧组件，<br>
这个过程中：
    1. 没有修改过旧组件，所以旧组件不会对新功能产生依赖
    2. 不会有命名上的冲突
    3. 因为1的缘故，新增代码集中在一处（删改更容易）

高阶组件还有这个用途：
* 可以使用高阶组件来来将context转换为props，<br>
避免了对context的api的依赖

另外还有函数子组件。



## 第5章 恰当地获取数据
介绍了一个用高阶组件，将数据获取逻辑从组件中提取出来的模式


## 第6章 为浏览器编写代码
一上来的就是一个应付多字段表单的模式：
```javascript
class Form extends React.Component {
    constructor(props) {
        super(props)
        this.state = {
            firstName: '',
            lastName: '',
        }
        this.handleChange = this.handleChange.bind(this)
        this.handleSubmit = this.handleSubmit.bind(this)
    }
    handleChange({ target }) {
        this.setState({
            [target.name]: target.value
        })
    }
    handleSubmit(e) {
        e.preventDefault()
        console.log(`${this.state.firstName} ${this.state.lastName}`)
    }
    render() {
        return (
            <form onSubmit={this.handleSubmit}>
                <input
                    type="text"
                    name="firstName"
                    onChange={this.handleChange}
                />
                <input
                    type="text"
                    name="lastName"
                    onChange={this.handleChange}
                />
                <button>Submit</button>
            </form>
        )
    }
}
```
还有:
* 用JSON schema生成表单的方法
* 事件（合成事件会被回收，并且存在唯一的全局处理器（即事件代理））
* ref（示例里面是用ref注册一个回调，然后回调会修改当前对象的字段）
* 动画（我总觉得动画要另外声明一个组件非常的麻烦，其中react-motion值得去深入了解一下）
* svg（react支持svg元素的生成，这一点很好）



## 第7章 美化组件
css的主要问题（值得思考）：
1. 全局命名空间（css全局可见）
2. 依赖
3. 无用代码移除（1引起的，删除东西你不知道会影响到什么地方）
4. 压缩（类名的重复、它们想要把html上重复的类名也做压缩）
5. 常量共享（某些地方需要js运算计算值）
6. 解析方式不确定（css顺序和加载顺序打架）
7. 样式隔离（仍然是1引起的）

为了解决上面的这些问题，他们用起了行内样式<br>
但是带来了更多的问题：
1. 服务器端生成的html变大了。
2. 用不了伪类、伪元素、媒体查询。
3. 用不了样式回退。
4. 覆盖样式要用!important

为了解决这些新问题，开发了这些库：
* Radium：在满足特定的条件下给元素用js添加相应的样式，以支持伪类、伪元素、媒体查询

也要webpack提供的方法：
* css-loader的modules功能（这个东西和vue的scoped css差不多的原理）

还有一个流行的库Styled Component（本篇没有太多的描述，值得深入了解）

## 第8章 服务端渲染的乐趣与益处

使用服务端渲染的原因:
1. seo
2. 通用代码库
3. 性能更强（首屏渲染）

这里介绍的第一个方法和《同构JavaScript应用开发》里面的基本相同，<br>
都是先在客户端先请求好数据，然后把数据放在一个全局变量里面。<br>
(我觉得这个方法是在是太麻烦了，最好就是前后端的代码结构完全一样的)

第二个方法（Next.js）就很好了，<br>
只需要在对应的组件类里面添加一个static async function钩子来获取数据，<br>
通过使用isomorphic-fetch这样的同构数据请求方法，就可以在前后端共享完全一样的代码。

## 第9章 提升应用性能

1. key属性
2. webpack构建时的production模式，UglifyJs压缩
3. shouldComponentUpdate通过对比state、props，阻止render函数的执行<br>
（extends自React.PureComponent时，该方法有默认实现）
4. 传递给组件的方法，应该在constructor里面先用bind绑定一下上下文（上面提到过）
5. 大组件分解成小组件（主要是阻止一小部分的数据变化引起了一整个大组件的重新渲染）

其它的一些工具：
1. immutable.js可以编写更完善的shouldComponentUpdate（默认的只做浅比较）
2. react-perf-tool（自动化的Perf 插件）
3. Babel 插件：
    * babel-plugin-transform-react-constant-elements：React 常量元素转换器
    * babel-plugin-transform-react-inline-elements：React 行内元素转换器

## 第10章 测试与调试
测试框架需要以下功能：
* 获取组件渲染后的vdom
* 获取组件渲染后的dom
* 查找dom的方法
* 模拟事件发生的方法
* 生成mock的方法

（感觉jquery之类的应该非常好用）

react独有的：
* 测试快照：保存第一次测试的vdom，和本次进行对比

最后有一个react组件渲染发生异常时，会一整个组件都渲染不出来，<br>
react-component-errors库会给所有的组件方法加上猴子补丁，<br>
并封装到try...catch语句中，这样就不会导致整棵树渲染失败。<br>
(这个东西有一定的参考意义)


