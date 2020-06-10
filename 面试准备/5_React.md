# 面试准备5 - React 

## 1、Fiber
> 解决大量组件渲染，导致主线程被占用，采用**任务分割**的方式。

简述：
* react16，更改了调度方式，由原来的`栈调度`变为`fiber调度`，编程具有链表形式的结构，每个单元具有`return`,`child`,`sibling`，可以记录当前遍历的节点，从而可以中断。并将每个小任务分散到浏览器空闲时间执行。
* 遍历方式：同步递归 ==> 任务分割
* **链表树遍历算法：**
    * 有子节点就遍历至子节点到没有子节点为止。
    * 开始遍历兄弟节点。
    * return返回父节点，开始遍历兄弟节点。
    * 直到遍历完成。
* **任务分割：**
    * reconciliation阶段：
    * commit阶段：

## 2、生命周期
### 新的变动：
* componentWillMount ==> getDerivedStateFromProps
* componentWillUpdate ==> getSnapshotBeforeUpdate
* 避免使用componentWillReceiveProps

### 变动原因：
在reconciliation阶段，进行了任务分割，涉及暂停、重启，因此导致生命周期函数在一次更新渲染中被**多次调用**。

### 使用建议
```
// 初始化和 update 时被调用
static getDerivedStateFromProps(nextProps, prevState) {}
// 判断是否需要更新组件
shouldComponentUpdate(nextProps, nextState) {}
// 组件被挂载后触发
componentDidMount() {}
// 可以在更新之前获取最新 dom 数据
getSnapshotBeforeUpdate() {}
// 组件更新后调用
componentDidUpdate() {}
// 组件即将销毁
componentWillUnmount() {}
// 组件已销毁
componentDidUnMount() {}
```

## 3、setState
> 用于修改状态，更新视图。

### 实现
* 更新策略处于事物流中执行，组件会被推入dirtyComponents中等待执行。
* 否则，开始执行batchedUpdates队列更新。

### 特点：
#### 3.1、同步/异步：与事件调用环境有关
* 在合成事件和生命周期钩子（除componentDidUpdate），setState是"异步"的。
    * 生命周期钩子：更新策略处于更新之前，组件仍处于事物流中，则需要等待执行。
    * componentDidUpdate生命周期钩子：更新策略在更新之后，组件不处于事物流，则同步执行。
    * 合成事件：react是基于 事务流完成的事件委托机制，处于事物流中，则需要等待执行。
    * ==> 解决，this.setState()的第二个参数可获取最新值。
* 在原生事件/setTimeout ，setState是"同步"的，可以立刻获取到最新值。
    * 原生事件：是基于浏览器，和事物流无关
    * setTimeout：放于定时器线程中延后执行，此时事物流已结束

#### 3.2、批量更新：Object.assign()
#### 3.2、建议使用函数式：this.setState((state, props) => newState)
* 避免批量更新逻辑，传入的函数按照顺序调用

## 4、HOC组件
> 输入一个组件，返回一个增强的组件

### 应用场景
* 权限控制
* 性能监控
* 代码复用

## 5、redux
> 数据管理中心。

### 核心理念
* 单一数据源
* 状态只读
    * 数据无法被直接修改
    * 严格控制修改的执行
* 纯函数：通过纯函数描述数据的修改
* Store ==> Store.getState() ==> Store.dispatch({type, playload}) ==> Action ==> Action.type ==> Reducer(state, action) ==> newState

### 实现
#### Store
* Store.getState(): 获取数据
* Store.dispatch(): 触发action，更新数据
* Store.subscribe(): 订阅数据更新，注册监听器
```
var store = Redux.createStore(Reducer, initStore)
```

#### Action
```
const action = {
    type: 'BODY_INIT',
    data: [1,2,3],
}
store.dispatch(action)
```

#### Reducer：修改数据的纯函数
```
function ListReducer(state = [], action) {
	switch (action.type) {
		case 'BODY_INIT':
			return state.concat([action.data])
			break
		defalut:
			return state
	}
}
```

### react-redux
#### Provider：容器组件，接收store
#### connect：高阶组件
* mapStateToProps: 将state赋值给props，赋值给组件
* mapDispatchToProps: 将方法挂在props上

#### combineReducers：将reducer合并
#### 异步action中间件：
* redex-thunk
* redux-saga
* redux-observable

### 6、React Hooks
> 函数组件中使用react特性

#### 好处
* 跨组件复用：不改变原组件的情况下
* 状态与UI隔离

#### useState
```
// 只接收一个参数：初始状态
// 返回更改组件对应的函数
const [flag, setFlag] = useState(true);
```

#### useEffect ==> componentDidMount、componentDidUpdate和componentWillUnmount的结合。
```
// useEffect(callback, [source])
// source: 设置触发条件，仅当 source 发生改变时才会触发；
// useEffect钩子在没有传入[source]参数时，默认在每次 render 时都会优先调用上次保存的回调中返回的函数，后再重新调用回调；
useEffect(() => {
	// 组件挂载后执行事件绑定
	console.log('on')
	addEventListener()
	
	// 组件 update 时会执行事件解绑
	return () => {
		console.log('off')
		removeEventListener()
	}
}, [source]);
```

##### 第二个参数可模拟的几种生命周期
* componentDidMount: 传入[]时，就只会在初始化时调用一次；
```
const useMount = (fn) => useEffect(fn, [])
```
* componentWillUnmount: 传入[]，回调中的返回的函数也只会被最终执行一次；
```
const useUnmount = (fn) => useEffect(() => fn, [])
```
* mounted: 可以使用 useState 封装成一个高度可复用的 mounted 状态；
```
const useMounted = () => {
    const [mounted, setMounted] = useState(false);
    useEffect(() => {
        !mounted && setMounted(true);
        return () => setMounted(false);
    }, []);
    return mounted;
}
```
* componentDidUpdate: useEffect每次均会执行，其实就是排除了 DidMount 后即可；
```
const mounted = useMounted() 
useEffect(() => {
    mounted && fn()
})
```

#### 其他内置钩子
* useContext
* useReducer
    * 不是持久存储，会随着组件销毁而销毁
    * 属于组件内部，不能共享
    * 可以配合useContext完成一个轻量的redux
* useMemo：用于缓存传入的 props，避免依赖的组件每次都重新渲染；
* useRef：获取组件的真实节点；
* useCallback：缓存回调函数，避免传入的回调每次都是新的函数实例而导致依赖组件重新渲染，具有性能优化的效果；
* useLayoutEffect
    * 等待DOM更新完后才会触发
    * 可以拿到更新后的state
* 自定义钩子（useXXX）

## 7、SSR
> 服务端渲染，在服务端渲染出完成的HTML返回给浏览器。

### 解决的痛点
* 页面白屏
* seo问题

## 8、函数式编程
* 纯函数
* 函数复合
* 数据不可变性