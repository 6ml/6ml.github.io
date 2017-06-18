---
layout: post
title: "初识Redux"
date: 2016-12-04
description: "初识Redux"
tag: Framework
---

### 基本概念
#### store
保存数据的容器，整个应用只有唯一的`store`
#### state
一个对象，保存所有数据，一个`state`对应一个view，只要`state`相同view就相同。
*--与*`react`*中的*`state`*不同*
#### action
对象，没有固定格式，但是`type`属性是必须的，可以参考[规范](https://github.com/acdlite/flux-standard-action)。由于View层无法接触到`state`，View层只能通过发出`action`表示要改变`state`
#### action creator
用来创建`action`的函数，返回`action`对象
#### store.dispatch()
view层发出`action`的唯一方法，接受一个`action`对象
#### reducer()
`store`接受了`action`后必须给出一个新的`state`，然后view才会改变，这个计算过程就叫`reducer`。`reducer`函数接受view层发出的`action`对象和当前`state`，返回一个新的`state`。
*--*`reducer`*是一个纯函数，同样的输入得到的输出必定是相同的。*
#### store.subcribe()
这个方法用于设置监听函数，一旦`state`改变，自动执行此函数，**返回一个函数**，调用该函数可以解除监听
#### createStore（）
redux提供的原生方法，用于创建`store`。在`createStore`中传入`reducer`和`dispatch`后，`reducer`会自动被调用。
#### getState()
在store中取得当前state快照的方法。
#### combineReducer（）
大型应用`state`太大，用一个`reducer`的话太大，可将`reducer`拆分成多个`reducer`，最后用`combineReducer`合并成一个`reducer`。

**拆分方式**是看`action`，如果`action`分别改变不同的`state`属性，那么就可以把`reducer`函数拆分成对应的`reducer`函数。

接受一个对象作为参数，`state`对象的结构由传入的多个`reducer`的`key`决定。对象的**key**对应`state`中的**属性名**，对应的**value**是拆分了的`reducer`函数。**但是如果在**`createStore`**中提供了第二个参数**`defaultState`**，它会覆盖**`reducer`**函数的默认初始值**。
### 中间件
引入中间件的原因是我们无法进行异步操作，view层发出`action`，`store.dispath`接受`action`对象，`reducer`立即算出`state`，即可更新view层。这里`reducer`立即算出`state`，这是**同步**；我们想要`action`发出后过一段时间再执行`reducer`，这就是**异步**。
#### 中间件的概念
只有发出`action`这个步骤，即`store.dispatch`，可以添加功能。我们举例来改造dispatch，添加日志功能。
```
let next = store.dispatch;

store.dispatch = function dispatchAndLog(action){

    console.log('dispatching',action);
    
    next(action);
    
    console.log('next state',store.getState());

}
```
中间件就是一个函数，对`store.dispatch`进行改造，在发出`action`和执行`reducer`这两步之间添加其他功能。
#### 中间件的用法
上一节的日志中间件，有现成的`redux-logger`模块，我们来看看怎么使用它。
```
import { applyMiddleware, createStore } from 'redux';

import CreateLogger from 'resux-logger';

const logger = CreateLogger();

const store = createStore(

    reducer,
    
    applyMiddleware(logger)
    
)
```
我们将日志中间件`logger`放入`applyMiddleware`中，传入`CreateStore`方法，就完成了对`store.dispatch`的功能增强。
**要注意两点**
如果`createStore`要接受初始状态`defaultState`的话，`applyMiddleware`就是第三个参数了。
有些中间件的次序有讲究，比如logger必须放在最后。
```
applyMiddleware(thunk, promise, logger)
```
#### applyMiddlewares
它是redux的原生方法，作用是将所有中间件组成一个数组，依次执行。下面是它的源码。
```
export default function applyMiddleware(...middlewares) {

    return (createStore) => (reducer, preloadedState, enhancer) => {
    
        var store = createStore(reducer, preloadedState, enhancer);
        
    	var dispatch = store.dispatch;
    
    	var chain = [];

    	var middlewareAPI = {
        
            getState: store.getState,
            
      		dispatch: (action) => dispatch(action)
            
    	};
    
    	chain = middlewares.map(middleware => middleware(middlewareAPI));
        
    	dispatch = compose(...chain)(store.dispatch);

        return {...store, dispatch}
    
  	}
  
}
```
我们将所有中间件都放入了一个数组`chain`，然后嵌套执行，最后执行`store.dispatch`。中间件内部可以拿到`getState`和`dispatch`这两个方法。
#### 异步操作的基本思路
#### redux-thunk
正常情况下`store.dispatch`只能接受对象作为参数，不能是函数。这时，中间件[`redux-thunk`](https://github.com/gaearon/redux-thunk)就能帮到我们了。
```
import { createStore, applyMiddleware } from 'redux';

import thunk from 'redux-thunk';

import reducer from './reducers';

// Note: this API requires redux@>=3.1.0
const store = createStore(

    reducer,
  
    applyMiddleware(thunk)
  
);
```
这样`store.dispatch`方法就能接受函数作为参数了。
#### redux-promise
### react-redux
#### UI组件
React-Redux将所有组件分为两大类：UI组件 (presentational component) 和容器组件 (container component) 。
UI组件特征
-	只负责 UI 的呈现，不带任何业务逻辑
-	没有状态（即不使用`this.state`这个变量）
-	所有数据都有参数（`this.props`）提供
-	不使用任何 Redux 的 API

因为不含有状态，UI组件又称“纯组件”。
#### 容器组件
容器组件的特征恰恰相反。
-	负责管理数据和业务逻辑，不负责 UI 的呈现
-	带有内部状态
-	使用 Redux 的 API

React-Redux规定，所有的 UI 组件都由用户提供，容器组件则由 React-Redux 自动生成。
#### connect()
`connect`方法用来从 UI 组件生成容器组件。
```
import { connect } from 'react-redux';

import { Main } from './Main';

const App = connect()(Main);
```
`Main`是 UI 组件，`App`就是通过`connect`方法自动生成的容器组件。

`connect`还需接受两个参数，`mapStateToProps`和`mapDispatchToProps`。它们定义了 UI 组件的业务逻辑，前者负责输入逻辑，即将`state`映射到 UI 组件的参数(`props`)，后者负责输出逻辑，即将用户对 UI 组件的操作映射成`Action`。
#### mapStateToProps()
这个函数最后**返回一个对象**，里面的每一个键值对就是一个映射。
```
const mapStateToProps = (state) => {

    return {
    
        todos: state.todos;
    
    }

}
```
`mapStateToProps`会订阅Store，每当`state`更新，就会自动执行，重新计算 UI 组件的参数，从而出发 UI 组件的重新渲染。

`mapStateToProps`还可以接受第二个参数，代表容器组件的`props`对象。

使用`ownProps`作为参数后，如果容器组件的参数发生变化，也会引发 UI 组件重新渲染。

当然也可以省略`mapStataToProps`参数，那样 UI 组件就不会订阅Store。
#### mapDispatchToProps
这是`connect`的第二个参数，它定义了哪些用户操作应当作Action传给Store。**它可以是函数，也可以是对象**。

-	当它是函数时，会得到`dispatch`和`ownProps`两个参数。**返回一个对象**，该对象的每个键值对都是一个映射，定义了 UI 组件的参数怎样发出 Action。

```
const mapDispatchToProps = (dispatch, ownProps) => {

    return {
    
        onClick: () => {
        
            dispatch({
            
                type: 'SET_VISIBILITY_FILTER',
                
                filter: ownProps.filter
            
            })
        
        }
    
    }

}
```
-	当它是对象，它的**每个键名也是对应 UI 的同名参数，键值应该是一个函数，会被当做 Action Creator **，返回的 Action 会由 Redux 自动发出。

```
const mapDispatchToProps = {

    onClick: (filter) => {
    
        type: 'SET_VISIBILITY_FILTER',
        
        filter
    
    }

}
```

我们也可以不写`mapDispatchToProps`的内部函数，可以使用 Redux 的`bindActionCreators`来帮助我们完成这个函数。
```
import { bindActionCreators } from 'redux';

import * as actionCreators from './actions/actionCreators';

function mapDispatchToProps(dispatch){

    return bindActionCreators(actionCreators, dispatch);

}
```
#### &lt;Provider&gt;组件
`connect`方法生成容器组件后，需要让容器组件拿到`state`对象，才能生成 UI 组件的参数。
有两种方法可以让容器组件拿到`state`对象，一种是将`state`对象作为参数传给容器组件，但是这样做很麻烦，尤其是容器组件在很深的层级，一级级往下传很不方便。
React-Redux提供了`Provider`组件，可以让容器组件拿到`state`。
```
import { Provider } from 'react-redux;

import store from './store';

import App from './components/App';

ReactDOM.render(

    <Provider store={store}>
    
        <App />
        
    </Provider>,
    
    document.getElementById('root')
    
)
```
在根组件外层用`Provider`包起来，这样一来，`App`的所有子组件就默认拿到`state`了。
#### React-Router
这是一个路由库，使用时将`Router`放在根组件外层即可，但是要注意`Provider`要放在最外层，因为`Provider`的唯一功能就是传入`state`对象。
```
const Root = ({store}) => (

    <Provider store={store}>
    
        <Router>
        
            <Route path="/" component={App}>
            
                <IndexRoute component={PhotoGrid}></IndexRoute>
                
                <Route path="/view/:postId" component={Single}></Route>
                
            </Route>
            
        </Router>
        
    </Provider>
    
)
```
`App`是根组件，`PhotoGrid`和`Single`都是它的子组件，默认路由是`PhotoGrid`。

React-router 还有一个`Link`标签，可以跳转到`to`属性指定的地方。
```
<Link to={`/view/${post.code}`}>

    <img src={post.display_src}>
    
</Link>
```
### 工作流
用了 React-Redux 以后的工作流：
1. 创建 UI 组件
2. 创建`ActionCreator`，包含将要转化成容器组件`props`的函数、业务逻辑
3. 创建`reducers`，根据不同`action.type`进行不同的新的`state`计算的函数
4. `combineReducer`来合并 reducers ，生成`rootReducer`以便在`createStore`时传入参数
5. 创建根组件，用`mapStateToProps`函数将`state`转化成容器组件的`props`，用`mapDispatchToProps`将`dispatch`转化成容器组件的`props`(使用`bindActionCreators`，`bindActionCreators`方法中传入`actionCreators`和`dispatch`,最后返回`bindActionCreators`)，用`connect`方法来生成容器组件
6. 创建`store`，包含 [ 初始状态 ] 、合并后的`reducer`、[ 中间件 ]
7. 将`store`传入`Provider`的`store`属性中，路由、根组件放入`Provider`中

![基本流程图](/images/posts/redux/basic_flow.png)
**注意：**`actionCreators`**中的函数对应 UI 组件**`props`**中的方法；**`combineReducer`**方法中的对象**`key`**对应 UI 组件**`props`**中的属性，**`value`**对应**`reducer`**函数。**

> 参考文档：
-	[Redux 入门教程（一）：基本用法](http://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_one_basic_usages.html) 作者：阮一峰
-	[Redux 入门教程（二）：中间件与异步操作](http://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_two_async_operations.html) 作者：阮一峰
-	[Redux 入门教程（三）：React-Redux 的用法](http://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_three_react-redux.html) 作者：阮一峰