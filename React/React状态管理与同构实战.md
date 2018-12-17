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
    3. 至于Provider，这东西是来搞笑的么？<br>
    （再看了一下，不是搞笑，connect的使用实在是太频繁了，必须把state绑到context上才能避免不断的重复引入state）



## 第5章 揭秘React同构应用
* 同构的优缺点
    1. 更好的性能（首屏直出、对低端机友好、恶劣网络友好、更好的用户体验）
    2. seo优化支持
    1. 复杂性
    2. 前后无法完全复用
    3. 增加了服务端TTFB

* api:
    renderToString：
        旧：生成的html上会有data-react-checksum属性，该属性的值由该dom节点的vdom结构hash出来，用于前端校验是否需要重新渲染。
        新：新api不会生成data-react-checksum，原理未知
    renderToNodeStream：以流的方式输出html字符串

* 其他事项：
    1. 服务端上只有componentDidMount之前的生命周期方法有效，在这些方法上不能用浏览器独有api（localStorage、window等，应该在componentDidMount上用）
    2. 对于数据拉取，典型做法是把请求逻辑放到React组件的静态方法中，可让前后复用（貌似是Next.js的做法）
    3. isomorphic-fetch实现前后端一致的拉取api
    4. 实在不能复用就if-else

* 套路（和《同构JavaScript应用开发》中介绍的一样）：
    1. 服务器拉取数据，修改了store
    2. store序列化，保存在一个全局变量里面（要注意xss）
    3. 用store在服务端渲染
    4. 在客户端用那个全局变量恢复store
    5. 前端渲染、进行校验

* Next.js的demo（实现原理和上面的套路一样、还有些其他很复杂的功能）：
    1. next内置了webpack和express
    2. 路由分割使用的一个约定，放在pages文件夹下的一个文件对应一个页面
    3. 用static async getInitialProps钩子实现前后端通用的数据拉取，钩子的参数是req, query之类的，**而且只有在页面组件里面才会被调用**
    4. 钩子可以作为无状态组件（同时是一个function）的属性被调用
    5. 提供Link元素，可作前端跳转，还支持预加载
    6. 。。。



## 第6章 深入理解React技术内幕与生态社区
* 高阶组件的注意事项：
    1. 不可直接修改接收到的组件的自身行为。
    2. 保证没有副作用
    3. 一般通过添加props的形式给原有组件传递信息
    4. 不要在render方法中使用高阶组件（搞不懂）
    5. 高阶组件不会传递refs（搞不懂）

* mixins的问题：
    1. 不确定性：会直接修改state，且修改来源不确定。
    2. 命名冲突。

* 高阶组件的问题：
    1. 不确定性：state的修改被转移到了props但仍然存在。
    2. 命名冲突：如果两个高阶组件同时扩充一个同名prop问题仍然存在

* 推荐用render prop、Function as Child Component:
    * props.children是一个函数，的有控制反转的组件。
    * 弊端：性能问题，因为每一次渲染都生成了一个新函数

* 要把组件渲染到父节点之外：
    1. 旧方法：render返回null，另外有一个方法用ReactDom.render把节点渲染到别的地方，需要绑定DidMount，WillUpdate，WillUnmount等钩子
    2. 新方法：
    ```javascript
    render() {
        return ReactDOM.createPortal(
            this.props.children,
            anyDomNode
        )
    }
    ```


* 对于setState方法这个现实，如何实现连续的state修改：
    1. this.setState((prevState, props) => ({
        count: prevState.count + 1
    }))
    2. 地狱回调
    3. 使用DidUpdate钩子（什么狗屎?）
    4. 包装setState返回promise




* diff算法降低时间复杂度的方法：
    1. dom节点跨层级的移动操作特别少（只对同一层级进行比较）
    2. 相同类型组件会生成相似的树形结构，不同的生成不同的（不同类型不做比较，直接替换）
    3. 同一层级可通过key进行区别（协助添加、删除、排序操作）

* redux最佳实践：
    1. 数据结构扁平化（复杂数据源用函数式处理起来超麻烦）
    2. 使用reselect类库（这个库应该是监听了state的变化，实现了一套像是vue里面computed的反应式系统）

## 第7章 单页面应用代码分割
分割代码的方式：
    1. 按照业务逻辑和依赖库分割（业务和库方别分割到不同文件）
    2. 按照路由分割
    3. 按照组件分割

按需加载：
    * 消极加载：不需要用户额外交互便进行加载
    * 积极加载：用户进行了额外交互才进行加载

组件懒加载原理：一个高阶组件，接收一个返回Promise<Component>的函数作为prop，<br>
WillMount时调用这个函数，在Promise.resolve后调用forceUpdate进行重新渲染

Redux reducer懒加载原理：store提供了replaceReducer方法，可整个替换reducer，<br>
通过使用combineReducer函数将旧reducer和懒加载新得到的reducer合并，在调用replaceReducer

## 第8章 React应用性能优化
react自带：
1. 高效的diff算法（上面有说）
2. Batch操作（一口气更新（setState异步））
3. 摒弃脏检测更新方式（shouldComponentUpdate）

其他：
1. React.addons.Perf
2. 和shouldComponentUpdate相关的
    * recompose的@pure
    * Redux connect的最后一个参数（提供各式state、props对比）
    * 消除render里面的新建数组、函数
    * PureComponent
    * redux-worker中间件(这个最有搞头了)