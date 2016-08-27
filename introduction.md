# 为何有这个库
- 现在单页应用等各种程序的各种复杂度，使得我们必须管理越来越多的状态，到了刻不容缓的地步
- 状态非常难管理，各种 model 关联着各种 model，你完全不懂更新这个 model 会引起别的 model 什么变化，你便对它们无法控制了
- 数据的变化性和异步性如何结合？这个问题一直困扰着人们，别怕，现在告诉你，Redux 就是为解决这个问题而生的

# 三大原则
## 1. 单一源
- 你的应用就应该只有一个状态树来管理
- 单一状态树好处多多，像什么更好的调试啊、实现撤销/重做啊等等

```js
console.log(store.getState())

/* Prints
{
  visibilityFilter: 'SHOW_ALL',
  todos: [
    {
      text: 'Consider using Redux',
      completed: true,
    },
    {
      text: 'Keep all state in a single tree',
      completed: false
    }
  ]
}
*/
```

## 2. 状态是只读的
- 改变状态的唯一方法就是传一个 `action`，它是一个 `object`
- 这样的好处很多啊，*在 state 前加一层 action 就可以做很多事啦*，

```js
store.dispatch({
  type: 'COMPLETE_TODO',
  index: 1
})

store.dispatch({
  type: 'SET_VISIBILITY_FILTER',
  filter: 'SHOW_COMPLETED'
})
```

## 3. 用纯函数去改变
- 你需要写一个纯 `reducer` 去改变状态树，它就是一个纯函数
- `reducers` 接收一个 `action` 和 之前的状态，然后返回一个新的状态对象

```js
function visibilityFilter(state = 'SHOW_ALL', action) {
  switch (action.type) {
    case 'SET_VISIBILITY_FILTER':
      return action.filter
    default:
      return state
  }
}

function todos(state = [], action) {
  switch (action.type) {
    case 'ADD_TODO':
      return [
        ...state,
        {
          text: action.text,
          completed: false
        }
      ]
    case 'COMPLETE_TODO':
      return state.map((todo, index) => {
        if (index === action.index) {
          return Object.assign({}, todo, {
            completed: true
          })
        }
        return todo
      })
    default:
      return state
  }
}

import { combineReducers, createStore } from 'redux'
let reducer = combineReducers({ visibilityFilter, todos })
let store = createStore(reducer)
```

# 现有技术
- Flux、Elm、Immutable、Baobab、Rx 都是非常棒的数据管理库，我们也混合了一些优点进来，所以我们是最叼的

# 生态
- 虽然这个库体积很小，但是它的 APIS 设计的非常仔细，因此有很多库可以与之相结合
- [详见生态列表](http://redux.js.org/docs/introduction/Ecosystem.html)

# 例子
- [非常多的例子来这里看吧](http://redux.js.org/docs/introduction/Examples.html)
