# 异步 Actions
- 实现异步 action 有一个很方便的方法就是使用 [redux-thunk](https://github.com/gaearon/redux-thunk) 这个 middleware
- redux-thunk 会让 action 返回的函数得到立即执行，thunk 的一个优点是它的结果可以再次被 dispatch
- redux-thunk 让 action 返回的函数的第一个参数是 dispatch，第二个参数是 getState，都是 store 的方法

```js
export function fetchPosts(subreddit) {

  // Thunk middleware knows how to handle functions.
  // It passes the dispatch method as an argument to the function,
  // thus making it able to dispatch actions itself.

  return function (dispatch) {

    // First dispatch: the app state is updated to inform
    // that the API call is starting.

    dispatch(requestPosts(subreddit))

    // The function called by the thunk middleware can return a value,
    // that is passed on as the return value of the dispatch method.

    // In this case, we return a promise to wait for.
    // This is not required by thunk middleware, but it is convenient for us.

    return fetch(`http://www.reddit.com/r/${subreddit}.json`)
      .then(response => response.json())
      .then(json =>

        // We can dispatch many times!
        // Here, we update the app state with the results of the API call.

        dispatch(receivePosts(subreddit, json))
      )

      // In a real world app, you also want to
      // catch any error in the network call.
  }
}
```

- 记得用 `applyMiddleware()` 去包含这个 middleware

```js
import thunkMiddleware from 'redux-thunk'
import createLogger from 'redux-logger'
import { createStore, applyMiddleware } from 'redux'
import { selectSubreddit, fetchPosts } from './actions'
import rootReducer from './reducers'

const loggerMiddleware = createLogger()

const store = createStore(
  rootReducer,
  applyMiddleware(
    thunkMiddleware, // lets us dispatch() functions
    loggerMiddleware // neat middleware that logs actions
  )
)

store.dispatch(selectSubreddit('reactjs'))
store.dispatch(fetchPosts('reactjs')).then(() =>
  console.log(store.getState())
)
```

# 中间件（未完待续）
- redux 中间件提供了在 action 和 reducer 之间的扩展，可以实现日志记录、创建崩溃报告、调用异步接口或者路由等
