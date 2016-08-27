# Action
- action 它装载着数据信息，是 store 数据的唯一来源，用 `store.dispatch()` 方法来发送他们

```js
const ADD_TODO = 'ADD_TODO'
```

```js
{
  type: ADD_TODO,
  text: 'Build my first Redux app'
}
```

- action 是一个纯对象，应该让它有一个 type 属性来标识它应该是干嘛的
- 如果项目很大，type 应该定义为字符串常量，可以更好的让它们具有唯一性

# Action 创建器
- 一个纯函数而已，返回一个对象，推荐试用创建器，这样可以使得它能移植和易于测试

```js
function addTodo(text) {
  return {
    type: ADD_TODO,
    text
  }
}
```

- 或者可以创建一个 `bound action creator`，这样就可以自动触发了

```js
const boundAddTodo = (text) => dispatch(addTodo(text))
const boundCompleteTodo = (index) => dispatch(completeTodo(index))

boundAddTodo(text)
boundCompleteTodo(index)
```

# Reducers
- reducer 的工作就是在响应里指定 state 如何变化

# 设计 state
- state 树尽量扁平化，像数据库一样，减少嵌套，如何规范可以看[这里](https://github.com/paularmstrong/normalizr)

# 处理 action
- reducer 就是个纯函数，传入之前的 state 和 action，然后返回新的 state

```js
(previousState, action) => newState
```

- reducer 永远要保持纯净，千万别：修改它的参数、执行有副作用的操作（API 调用、路由跳转等）、调用不纯的函数（Date.now()、Math.random() 等），反正就是每次进来相同的参数就会是相同的返回值

```js
function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return Object.assign({}, state, {
        visibilityFilter: action.filter
      })
    default:
      return state
  }
}
```

# 分割 reducers
- 可用 redux 的 `combineReducers()` 方法连接 reducers

```js
import { combineReducers } from 'redux'

const todoApp = combineReducers({
  visibilityFilter,
  todos
})

export default todoApp
```

# Store
- store 是一个对象，把 action 和 reducer 结合起来
- 它的职责有：承载应用的 state，提供 `getState()` 方法获取 state，提供 `dispatch(action)` 方法更新 state，通过 `subscribe(listener)` 注册监听器，通过 `subscribe(listener)` 返回的函数注销监听器
- 一个 redux 应用只有一个单一的 store
- 用 `createStore()` 来创建

```js
import { createStore } from 'redux'
import todoApp from './reducers'
let store = createStore(todoApp)
```

# 触发 actions
- 一切都在代码中：

```js
import { addTodo, toggleTodo, setVisibilityFilter, VisibilityFilters } from './actions'

// Log the initial state
console.log(store.getState())

// Every time the state changes, log it
// Note that subscribe() returns a function for unregistering the listener
let unsubscribe = store.subscribe(() =>
  console.log(store.getState())
)

// Dispatch some actions
store.dispatch(addTodo('Learn about actions'))
store.dispatch(addTodo('Learn about reducers'))
store.dispatch(addTodo('Learn about store'))
store.dispatch(toggleTodo(0))
store.dispatch(toggleTodo(1))
store.dispatch(setVisibilityFilter(VisibilityFilters.SHOW_COMPLETED))

// Stop listening to state updates
unsubscribe()
```

# 数据流
- redux 的结构核心是 **严格的单向数据流**，这样可以使你的应用更加可预测和理解
- 在 redux 应用里数据的生命周期遵循 4 个步骤：

1 调用 `store.dispatch(action)`，可以在应用的任意地方调用

2 redux store 开始调用 reducer 函数，store 将传 2 个参数给 reducer：当前的 state 树和 action

```js
// The current application state (list of todos and chosen filter)
 let previousState = {
   visibleTodoFilter: 'SHOW_ALL',
   todos: [
     {
       text: 'Read the docs.',
       complete: false
     }
   ]
 }

 // The action being performed (adding a todo)
 let action = {
   type: 'ADD_TODO',
   text: 'Understand the flow.'
 }

 // Your reducer returns the next application state
 let nextState = todoApp(previousState, action)
```

3 根 reducer 将会合并多个 reducers 到一个单 state 树里

4 redux store 将保存根 reducer 返回的完整 state 树，每个注册的监听器 `store.subscribe(listener)` 将被调用，这时监听器可以调用 `store.getState()` 来获取当前 state

# 配合 React 使用
- 它和 react 配合的非常棒，需要安装 react-redux
- 组件应该设计为容器组件和展示组件
- 搭配示例请看[这儿](http://redux.js.org/docs/basics/ExampleTodoList.html)
