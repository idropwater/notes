## 组件通信
- 父传子：props，单向
- 子传父：回调／自定义事件
- 跨级：context
- 没有嵌套关系：自定义事件（nodejs的events模块，发布／订阅模式）

## 组件抽象
- mixin：破坏原有组件封装，已弃
	
	```
	const mixin = function (obj, mixins) {
	    const newObj = obj
	    newObj.prototype = Object.create(obj.prototype)
	
	    for (const prop in mixins) {
	        if (mixins.hasOwnProperty(prop)) {
	            newObj.prototype[prop] = mixins[prop]
	        }
	    }
	    return newObj
	}
	
	// usage
	React.createClass({
		mixins: [PureRenderMixin],
		render() {
			return <div></div>
		}
	})
	```

- decorate：装饰
	
	```
	function handleClass(target, mixins) {
	    if (!mixins.lenght) { throw(); }
	    
	    for (let i = 0; i < mixins.length; i++) {
	        const descs = Object.getOwnPropertyDescriptor(mixins[i])
	        for (const key in descs) {
	            if (!(key in target.prototype)) {
	                Object.defineProperty(target.prototype, key, descs[key])
	            }
	        }
	    }
	}
	
	export default function mixin (…mixins) {
	    return (target) => {
	        return handleClass(target, mixins)
	    }
	}
	
	// usage
	const mixins = {
	    shouldComponentUpdate() {}
	}
	
	@mixin(mixins)
	class myComponent extends React.Component {
	    render() {}
	}
	
	```

- high-order component：高阶组件
	- @PureRenderMixin  ‘react-addons-pure-render-mixin’库
   	- @CSSMoudles   ‘react-css-modules’库

	```
	function logProps(WrappedComponent) {
	  return class extends React.Component {
	    componentWillReceiveProps(nextProps) {
	      console.log('Current props: ', this.props);
	      console.log('Next props: ', nextProps);
	    }
	    render() {
	      return <WrappedComponent {...this.props} />;
	    }
	  }
	}
	
	@logProps
	class myComponent extends React.Component {
	    render() {}
	}
	```

## 虚拟DOM
- 组成：标签名type、节点属性props、子节点、标示ID..

	```
	var ReactElement = function(type, key, ref, self, source, owner, props) {
	  var element = {
	    // This tag allow us to uniquely identify this as a React Element
	    $$typeof: REACT_ELEMENT_TYPE,
	
	    // Built-in properties that belong on the element
	    type: type,
	    key: key,
	    ref: ref,
	    props: props,
	
	    // Record the component responsible for creating this element.
	    _owner: owner,
	  };
	
	  return element;
	};
	```

- 创建虚拟元素：通过jsx创建虚拟元素会调用ReactElement.createElement(type, config, children) -> ReactElement(type, key, ref, ...)

- 虚拟dom类型：主要有三类
	- ReactText
	- ReactDOMElement
	- ReactComponentElement

- 组件入口类：与上面三类虚拟dom节点对应
	- ReactDOMTextComponent
	- ReactDOMComponent
	- ReactCompositeComponent 

- 初始化组件入口：instantiateReactComponent(node)
	- 通过`虚拟元素node`的类型区分不同组件的入口
	- 三个组件类都有一些相同名称的方法construct(对虚拟dom进行初始化)、mountComponent(把虚拟DOM转化为真正的DOM)、unmountComponent(卸载虚拟dom)、receiveComponent(接收虚拟dom的下一个状态)
	- 此处将虚拟元素`ReactElement`与组件`ReactComponent`联系起来
	- ReactEmptyComponent: `node === null || node === false`
	- ReactDOMComponent: `type node === 'object' && type node.type === 'string'`
	- ReactTextComponent...
	- ReactConpositeComponent...
	- 最后设置实例`instance.construct(node)`

## 生命周期
- 挂载
    - componentWillMount，componentDidMount只在初始化运行一次
    - constructor -> componentWillMount -> render -> componentDidMount

- 更新
    - this.setState 或者props发生变化时触发
    - componentWillReceiveProps -> componentShouldUpdate -> componentWillUpdate -> render -> componentDidUpdate

- 卸载
	- 移除原生事件活着定时器
	- componentWillUnmount

- createClass(spec)：编写自定义组件时就是调用该方法，利用原型继承ReactClassComponent,按顺序合并mixin，设置初始化defaultProps，返回构造函数

- 第一阶段MOUNTING
	- 方法：mountComponent
	- 由于递归渲染，父组件的componentWillMount在子组件componentWillMount之前调用，父组件的componentDidMount在子组件componentDidMount之后调用

- 第二阶段RECEVE_PROPS
	- 方法：updateComponent
	- 由于递归渲染，父组件的componentWillUpdate在子组件componentWillUpdate之前调用，父组件的componentDidUpdate在子组件componentDidUpdate之后调用

- 第三阶段UNMOUNTING
	- 方法：unmountCpomponent 

# setState机制
1. this.setState(newState)
2. newState存入pending队列
3. 如果是batchingStrategy.isBatchingUpdates为true保存组件到dirtyComponents中，否则遍历dirtyComponents调用updateComponent更新pending state or props

- Transaction事务

## redux
- 特点
	- 单一数据源，有利于对状态进行持久化，有利于做服务端渲染
	- 状态是只读的，不会自己定义store，而是createStore方法根据reducer生成store
	- 状态修改是通过reducer来完成的，它是一个纯函数，根据action和state返回一个全新的state

- 核心API
	- store = createStore(reducers[, initialState])

- middleware机制

	```
	function applyMiddleware(...middlewares) {
	  return createStore => (...args) => {
	    const store = createStore(...args)
	    let dispatch = () => {
	      throw new Error(
	        `Dispatching while constructing your middleware is not allowed. ` +
	          `Other middleware would not be applied to this dispatch.`
	      )
	    }
	
	    const middlewareAPI = {
	      getState: store.getState,
	      dispatch: (...args) => dispatch(...args)
	    }
	    const chain = middlewares.map(middleware => middleware(middlewareAPI))
	    dispatch = compose(...chain)(store.dispatch)
	
	    return {
	      ...store,
	      dispatch
	    }
	  }
	}
	
	function compose(...funcs) {
	  if (funcs.length === 0) {
	    return arg => arg
	  }
	
	  if (funcs.length === 1) {
	    return funcs[0]
	  }
	
	  return funcs.reduce((a, b) => (...args) => a(b(...args)))
	}

	```
	
## react-redux
- 核心API
	- `<Provider store>`：通过context向下传递props
	
	```
	// usage
	ReactDOM.render(
	  <Provider store={store}>
	    <MyRootComponent />
	  </Provider>,
	  rootEl
	)
	
	```
	- connect([mapStateToProps], [mapDispatchToProps], [mergeProps], [options])：该方法为高阶函数，最终将从store里的挑选出的state和actions里跳出的action作为props传给WrappedComponent
	
	```
	// usage
	import * as actionCreators from './actionCreators'
	import { bindActionCreators } from 'redux'
	
	function mapStateToProps(state) {
	  return { todos: state.todos }
	}
	
	function mapDispatchToProps(dispatch) {
	  return { actions: bindActionCreators(actionCreators, dispatch) }
	}
	
	export default connect(mapStateToProps, mapDispatchToProps)(TodoApp)
	
	```
	
## redux-thunk
- 源码

	```
	function createThunkMiddleware(extraArgument) {
	  return ({ dispatch, getState }) => next => action => {
	    if (typeof action === 'function') {
	      return action(dispatch, getState, extraArgument);
	    }
	
	    return next(action);
	  };
	}
	
	const thunk = createThunkMiddleware();
	thunk.withExtraArgument = createThunkMiddleware;
	
	export default thunk;
	
	```
	
## react-router
- 声明式路由
	