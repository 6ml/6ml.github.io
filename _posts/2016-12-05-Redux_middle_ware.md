---
layout: post
title: "Redux中间件"
date: 2016-12-05
description: "redux中间件的实现"
tag: Framework
---

### 什么是中间件
Redux允许你通过自定义的中间件来影响`store`的`dispatch`方法。

**定义**：中间件主要被用于分离那些不属于你应用的核心业务逻辑的可被组合起来使用的代码。

Redux的中间件主要用在`store`的`dispatch`函数上。`dispatch`函数的作用是发送`actions`给一个或者是多个`reducer`来影响应用状态的。中间件可以增强默认`diapatch`函数。Redux 1.0.1 版本的`applyMiddleware`源码：
```
export default function applyMiddleware(...middlewares) {

	return (next) => (reducer, initialState) => {
    
    	var store = next(reducer, initialState);
        
        var dispatch = store.dispatch;
        
        var chain = [];
        
        var middlewareAPI = {
        
            getState : store.getState,
            
            dispatch : (action) => dispatch(action)
        
        };
        
        chain = middlewares.map(middleware => middleware(middlewareAPI));
        
        diapatch = compose(...chain, store.dispatch);
        
        return{
        
            ...store,
            
            dispatch
        
        };
    
    };
    
}
```
### 函数式编程
#### 复合函数
函数式编程是非常理论化和数学化的。用数学的视角来解释复合函数：
```
given:
	
    f(x) = x^2 + 3x + 1
    
    g(x) = 2x
    
then:

    (f ∘ g)(x) = f(g(x)) = f(2x) = 4x^2 + 6x + 1
```
我们可以将上面的方式扩展到组合两个或者更多个函数这都是可以的。我们再来看个例子，演示组合两个函数并返回一个新的函数。

### Redux Middleware
Redux 中间件被设计成**可组合**的，会**在**`dispatch`**方法之前调用**的函数。

> 参考文档
- [深入浅出Redux中间件](http://www.tuicool.com/articles/u6JRjyz) 作者：Kazaff
