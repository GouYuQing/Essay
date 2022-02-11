# Vuex、Flux、Redux、Redux-saga、Dva、MobX

Vue，还是 React，都需要管理状态（state），组件之间有共享状态的需要，如果不进行管理，代码之间会很混乱

解决思路：**把组件之间需要共享的状态抽取出来，遵循特定的约定，统一来管理，让状态的变化可以预测**。所以产生了很多模式和库

**Stroe模式**

把状态存到外部变量中，

```
var store = {
  state: {//store 的 state 来存数据
    message: 'Hello!'
  },
  setMessageAction (newValue) {//store 里面有一堆的 action来控制 state 的改变，不直接去对 state 做改变，通过 action 来改变，因为都走 action，我们可以知道到底改变（mutation）是如何被触发的，出现错误，也可以记录记录日志
    this.state.message = newValue    // 发生改变记录点日志啥的
  },
  clearMessageAction () {
    this.state.message = ''
  }
}
```

这里没有限制组件不能修改store里面的state，必须设置不能直接修改store中的state，组件必须通过action来改变state。

组件里面应该执行 action 来分发 (dispatch) 事件通知 store 去改变

这样可以记录所有 store 中发生的 state 改变，同时实现能做到**记录变更 (mutation)、保存状态快照、历史回滚/时光旅行**的先进的调试工具。

**Flux**

类似于MVC，MVVM

![image-20200522203456554](images\image-20200522203456554.png)

*Dispatcher 的作用是接收**所有**的 Action，然后发给**所有**的 Store*

Flux的最大特点就是数据是**单向流动**

**Redux**

![image-20200522204758985](images\image-20200522204758985.png)

是flux的升级，flux中一个应用可以拥有多个 Store，多个Store之间可能有依赖关系；Store 封装了数据还有处理数据的逻辑

**Store**

Redux只有一个store，所有数据都在store中，Store 的 State 不能直接修改，每次只能返回一个新的 State。Redux 整了一个 createStore 函数来生成 Store

```
import { createStore } from 'redux';
const store = createStore(fn);
```

使用 store.subscribe 方法设置监听函数,然后state一改变就会自动执行函数更新view，实现自动渲染

**action**

View 发出的通知，告诉 Store State 要改变。

```
const action = {
  type: 'ADD_TODO',//代表action的名称，作为参数变更时候的参考
  payload: 'Learn Redux'
};
```

**reducer**

store.dispatch()是 View 发出 Action 的唯一方法

```
import { createStore } from 'redux';
const store = createStore(fn);
store.dispatch({//view 发出action
  type: 'ADD_TODO',
  payload: 'Learn Redux'
});
```

reducer是一个纯函数，处理state的，然后返回新的state，然后view变化

**纯函数**概念

```js
function getAge(user) {//有副作用，把user.age改变了,获取把返回值改变之类的都不是纯函数
  user.age = user.age + 1;
  return user.age;
}
```

对于相同的输入，永远都只会有相同的输出，不会被影响

```js
(previousState, action) => newState//根据应用的状态和当前的 action 推导出新的 state
//类比flux
（state,action）=>state
```

map 就是映射，reduce 就是归纳

映射就是把一个列表按照一定规则映射成另一个列表

 reduce 是把一个列表通过一定规则进行合并，也可以理解为对初始值进行一系列的操作，返回一个新的值。

比如Array的reduce方法：

```js
const array1 = [1, 2, 3, 4];
const reducer = (accumulator, currentValue) => accumulator + currentValue;
// 1 + 2 + 3 + 4
console.log(array1.reduce(reducer));
// expected output: 10
// 5 + 1 + 2 + 3 + 4
console.log(array1.reduce(reducer, 5));
// expected output: 15
```

比如

```js
const defaultState = 0;
const reducer = (state = defaultState, action) => {//接收参数
  switch (action.type) {
    case 'ADD':
      return state + action.payload;//返回新值
    default: 
      return state;
  }
};
```

createStore接受 Reducer 作为参数，生成一个新的 Store

```js
import { createStore } from 'redux';
const store = createStore(reducer);
//每当store.dispatch发送过来一个新的 Action，就会自动调用 Reducer，得到新的 State
```

createStore 内部干了什么

```js
const createStore = (reducer) => {//参数是reducer
  let state;//状态
  let listeners = [];//监听器？

  const getState = () => state;
  const dispatch = (action) => {//分发状态
    state = reducer(state, action);//返回新的状态
    listeners.forEach(listener => listener());//每个都监听
  };

  const subscribe = (listener) => {//订阅
    listeners.push(listener);//订阅监听
    return () => {
      listeners = listeners.filter(l => l !== listener);//返回
    }
  };

  dispatch({});

  return { getState, dispatch, subscribe };
};
```

如果是大型应用，state很庞大，所以reducer函数也很庞大，所以需要做拆分

Redux 里每一个 Reducer 负责维护 State 树里面的一部分数据，多个 Reducer 可以通过 combineReducers 方法合成一个根 Reducer，这个根 Reducer 负责维护整个 State

```js
import { combineReducers } from 'redux';
// 注意这种简写形式，State 的属性名必须与子 Reducer 同名
const chatReducer = combineReducers({//多个reducer管理多个小的state
  Reducer1,
  Reducer2,
  Reducer3
})
//combineReducers的实现
const combineReducers = reducers => {
  return (state = {}, action) => {
    return Object.keys(reducers).reduce(
      (nextState, key) => {
        nextState[key] = reducers[key](state[key], action);
        return nextState;
      },
      {} 
    );
  };
};
```

对比Flux

Flux：Store各自为战，为对应的View负责

![image-20200524213600130](images\image-20200524213600130.png)

Redux：由根Reducer统一管理

![image-20200524213703908](images\image-20200524213703908.png)

**中间件**

异步：action发出后，一段时间执行reducer

异步操作不能放在reducer中，纯函数不能承担异步操作，那样会被异步IO干扰，action就是一个纯对象，也没法放异步操作，

所以只能在 View 里发送 Action 的时候，加上一些异步操作了

```js
const store = createStore(
  reducer,
  applyMiddleware(thunk, promise, logger)//所有的中间件组成一个数组，依次执行。也就是说，任何被发送到 store 的 action 现在都会经过thunk，promise，logger 这几个中间件了
);
```

支持异步处理的中间件 [redux-thunk](https://link.zhihu.com/?target=https%3A//github.com/gaearon/redux-thunk) 或 [redux-promise](https://link.zhihu.com/?target=https%3A//github.com/acdlite/redux-promise)

redux-thunk把自主权交给了用户

```js
const createFetchDataAction = function(id) {
    return function(dispatch, getState) {
        // 开始请求，dispatch 一个 FETCH_DATA_START action
        dispatch({
            type: FETCH_DATA_START, 
            payload: id
        })
        api.fetchData(id) 
            .then(response => {
                // 请求成功，dispatch 一个 FETCH_DATA_SUCCESS action
                dispatch({
                    type: FETCH_DATA_SUCCESS,
                    payload: response
                })
            })
            .catch(error => {
                // 请求失败，dispatch 一个 FETCH_DATA_FAILED action   
                dispatch({
                    type: FETCH_DATA_FAILED,
                    payload: error
                })
            }) 
    }
}
//reducer
const reducer = function(oldState, action) {
    switch(action.type) {
    case FETCH_DATA_START : 
        // 处理 loading 等
    case FETCH_DATA_SUCCESS : 
        // 更新 store 等
    case FETCH_DATA_FAILED : 
        // 提示异常
    }
}
```

redux-promise：和redux-thunk相似，成功失败手动 dispatch 被封装成自动了，然后少了then和catch，被自动处理了，封装多了，代码简单了，用户自由度低了

```js
const FETCH_DATA = 'FETCH_DATA'
//action creator
const getData = function(id) {
    return {
        type: FETCH_DATA,
        payload: api.fetchData(id) // 直接将 promise 作为 payload
    }
}
//reducer
const reducer = function(oldState, action) {
    switch(action.type) {
    case FETCH_DATA: 
        if (action.status === 'success') {
             // 更新 store 等处理
        } else {
                // 提示异常
        }
    }
}
```

**Vuex**

![image-20200524220242011](images\image-20200524220242011.png)

**Store**

全局的store放着state，组件共享state

子组件能通过 this.\$store 访问到 state 了

```js
const app = new Vue({
  el: '#app',
  // 把 store 对象提供给 “store” 选项，这可以把 store 的实例注入所有的子组件
  store,
  components: { Counter },
  template: `
    <div class="app">
      <counter></counter>
    </div>
  `
})
const Counter = {
  template: `<div>{{ count }}</div>`,
  computed: {
    count () {
      return this.$store.state.count
    }
  }
}
```

state改变 view改变，视图改变，利用vue响应式机制

state不能直接修改，需要通过一个约定方式mutation，更改 Vuex 的 store 中的状态的唯一方法是提交 mutation

 mutation 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)

```js
const store = new Vuex.Store({//store.commit 方法去调用，mutation都是同步任务
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      // 变更状态
      state.count++
    }
  }
})
```

其实 Flux、Redux、Vuex**总的来说都是让 View 通过某种方式触发 Store 的事件或方法，Store 的事件或方法对 State 进行修改或返回一个新的 State，State 改变之后，View 发生响应式改变**

**action**

处理异步操作，Vuex的想法是把同步和异步拆分开

View 通过 store.dispatch('increment') 来触发某个 Action，Action 里面不管执行多少异步操作，完事之后都通过 store.commit('increment') 来触发 mutation，一个 Action 里面可以触发多个 mutation。所以 Vuex 的Action 类似于一个灵活好用的中间件。

**mutation 处理同步任务**和 **Action 处理异步任务**

vuex多个module，每个 Module 有自己的 state、mutation、action、getter，相当于一个大的store被拆分

**对比Redux**

Redux： view——>actions——>reducer——>state变化——>view变化（同步异步一样）

Vuex： view——>commit——>mutations——>state变化——>view变化（同步操作） 

view——>dispatch——>actions——>mutations——>state变化——>view变化（异步操作）

**Redux-saga**

中间件 redux-thunk 和 redux-promise把异步请求部分放在了 action creator 中

redux-saga没有把异步操作放在action creator中，它 把异步获取数据这类的操作都叫做副作用（Side Effect），它的目标就是把这些副作用管理好，让他们执行更高效，测试更简单，在处理故障时更容易

预备知识： ES6 的 [Generator](https://link.zhihu.com/?target=http%3A//es6.ruanyifeng.com/%23docs/generator)

如下：

```js
function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}
var hw = helloWorldGenerator();
hw.next() // 先把 'hello' 拎出来，done: false 代表还没完
// { value: 'hello', done: false } next() 方法有固定的格式，value 是返回值，done 代表是否遍历结束
hw.next() // 再把 'world' 拎出来，done: false 代表还没完
// { value: 'world', done: false }
hw.next() // 没有 yield 了，就把最后的 return 'ending' 拎出来，done: true 代表骂完了
// { value: 'ending', done: true }
hw.next() // 没有 yield，也没有 return 了，真的骂完了，只能挤出来一个 undefined 了，done: true 代表骂完了
// { value: undefined, done: true }
```

和async await异曲同工之妙

```js
function* gen(){
  var url = 'https://api.github.com/users/github';
  var jsonData = yield fetch(url);
  console.log(jsonData);
}

var g = gen();
var result = g.next(); 
// 这里的result是 { value: fetch('https://api.github.com/users/github'), done: true }

// fetch(url) 是一个 Promise，所以需要 then 来执行下一步
result.value.then(function(data){
  return data.json();
}).then(function(data){
  // 获取到 json data，然后作为参数调用 next，相当于把 data 传给了 jsonData，然后执行 console.log(jsonData);
  g.next(data);
});
```

 redux-saga ：每个 yield 都发起了阻塞，saga 会等待执行结果返回，再执行下一指令，变成了同步方法执行

**Dva**

基于 redux 和 redux-saga 的数据流方案,内置了 react-router 和 fetch，是一个轻量级应用框架

```js
app.model({
  // namespace - 对应 reducer 在 combine 到 rootReducer 时的 key 值
  namespace: 'products',
  // state - 对应 reducer 的 initialState
  state: {
    list: [],
    loading: false,
  },
  // subscription - 在 dom ready 后执行
  subscriptions: [
    function(dispatch) {
      dispatch({type: 'products/query'});
    },
  ],
  // effects - 对应 saga，并简化了使用
  effects: {
    ['products/query']: function*() {
      yield call(delay(800));
      yield put({
        type: 'products/query/success',
        payload: ['ant-tool', 'roof'],
      });
    },
  },
  // reducers - 就是传统的 reducers
  reducers: {
      ['products/query'](state) {
      return { ...state, loading: true, };
    },
    ['products/query/success'](state, { payload }) {
      return { ...state, loading: false, list: payload };
    },
  },
});
```

**MobX**

**任何源自应用状态的东西都应该自动地获得**

接近于面向对象编程，它把 state 包装成可观察的对象，这个对象会驱动各种改变

比如

```js
const obj = observable({
    a: 1,
    b: 2
})

autoRun(() => {
    console.log(obj.a)
})

obj.b = 3 // 什么都没有发生
obj.a = 2 // observe 函数的回调触发了，控制台输出：2
autoRun 就是这个老大哥，他看着所有依赖 obj.a 的地方，也就是收集所有对 obj.a 的依赖。当 obj.a 改变时，老大哥就会触发所有依赖去更新
```