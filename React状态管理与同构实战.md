# 《React状态管理与同构实战》
## 第1章 React与前端
* 声明式
* 基于组建
* 一次学习，多端受用

## 第2章 深入浅出React
* createClass与Component
* Functional Component
* PureComponent
* 生命周期：
    * 挂载：
        1. constructor
        2. componentWillMount
        3. render
        4. componentDidMount（组建加载数据）
    * 更新（父组件变化）：
        1. componentWillReceiveProps
        2. shouldComponentUpdate
        3. componentWillUpdate
        4. render
        5. componentDidUpdate
    * (自身变化)：
        1. shouldComponentUpdate
        2. componentWillUpdate
        3. render
        4. componentDidUpdate
    * （调用forceUpdate强制更新）
        1. componentWillUpdate
        2. render
        3. componentDidUpdate
    * 卸载：
        1. componentWillUnmount
* defaultProps和propTypes
* setState是异步函数
* 事件绑定是代理（这个怎么做到的有待研究）
* 组件通信：
    * 父子组件：
        1. props
        2. 事件回调
        3. EventEmitter
    * 爷孙组件：
        1. context
    * 兄弟组件：
        1. 通过父组件通信



## 第3章 Redux应用架构基础
* Redux设计哲学
    1. 单一数据来源<br>
    （意见保留，如果像是把所有的逻辑和数据都放到一处，<br>
    在大型app上对性能不利，因为所有的界面逻辑都被打包到一个文件了，<br>
    另外管理也是一个问题）
    2. 状态只读<br>
    （如果是单纯指组件不能修改store的值的话，这一点应该是不错的<br>
    因为数据的变化变得更容易追踪了）
    3. 纯函数生成变化<br>
    （这一点我不是很同意，本质上每一个状态会有一个vdom与之对应，<br>
    只要修改能够触发re-render来生成新的vdom树，那么函不函数式也没什么关系，<br>
    以函数式来生成新状态的好处是可以保留旧状态，可以用来做回退一类的动作）

* Redux的中间件的使用（这个东西值得研究一下）

* react-redux：
    * 容器组件
    * 展示组件

||展示组件|容器组件|
|---|---|---|
|目的|展示页面内容|处理数据和逻辑|
|是否感知Redux|不感知|感知|
|数据来源|从props获取|从Redux state订阅获取|
|改变数据|通过回调props派发action|直接派发action|
|由谁编写|开发者|由react-redux库产生|
