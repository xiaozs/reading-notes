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
    * 中间件的原理
    * redux-thunk通过劫持store的dispatch方法，使异步操作会根据其状态、结果依次发送不同的action

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

react-redux通过高阶组件将，原有的组件的props映射成store和action，<br>
以这种方式来进行容器组件和展示组件的解耦。




## 第4章 深入理解Redux（可能是本书最有用的一章）
* 介绍了redux的实现原理（这东西就是一个特殊一点的事件发射器），<br>
中间件功能跟我上面猜的一样是用的函数劫持的方式实现特殊功能的，<br>
而且是一层劫持上一层，直到原来的dispatch方法，<br>
因为这些中间件（其实就是一个柯里化函数）都可以调用上一层的dispath，<br>
行为方式就好像是服务器的中间件一样，所以才叫中间件。

* 使用redux的益处：
    1. 可以记录action和状态<br>
    （可以记录action是因为使用了单一数据源可以对每一个action进行拦截<br>
    状态的记录得益于函数式没有对原对象进行修改）
    2. 由于是一个action对应一个状态,<br>
    action和其对应的状态都完整的被记录了下来了，<br>
    一旦发生错误可以对状态进行回溯，便于调试
    3. 测试容易<br>
    （只有action能引起状态的变化，测试时只要触发action，并断言状态的改变是否符合预期即可）
    4. 其他：
        * 持久性（数据都存储在一个地方了，序列化一下就能保存，反序列化一下，然后把数据传给那些函数式组件就能完美地恢复）
        * 同构渲染（同上）
        * 记录用户session（就是前端的异常记录时能够拿到跳错时候的完整状态和触发异常的action，可以在本地恢复、调试）
        * 优化共享（就是多个组件监听同一个数据源，不会导致数据不一致）
        * 协同开发（搞不懂，是指redux啰嗦的八股文么）


* react-redux的原理：
    1. Provider组件把它自身的props映射到context上，<br>
    一般来说这个props会填入store
    2. connect高阶组件把store和action映射到原有的组件的props上

* react-redux的哲学：
    1. 鉴于UI层是最容易变化的，所以如果把业务逻辑和UI绑在一块，那一旦UI有变化，那就要改死人了<br>
    而且因为绑定了业务逻辑，这些UI组件就很难用到别的地方了。<br>
    所以UI和业务要分离（分离成了函数化组件和reducer，相当于mvc里面的视图层和控制器）<br>
    函数化组件很容易测试（提供数据，断言dom上是否显示了正确内容；触发事件，断言是否正确调用了回调）<br>
    reducer也很容易测试（提供数据，断言新生成的数据是否如预期）
    2. connect相当于是一个适配器层，用于指示视图层如何绑定控制器和数据源（分层很明确，非常好）
    3. 至于Provider，这东西是来搞笑的么？