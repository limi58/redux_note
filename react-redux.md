### react-redux 项目笔记

redux 栈的概念很多，用了之后如果不记下来的话，很快就忘了

#### connect([mapStateToProps], [mapDispatchToProps], [mergeProps], [options])

##### [mapStateToProps(state, [ownProps]): stateProps] (Function): 

如果指定了整个参数，组件会订阅 store 的更新，意思就是说，store 一旦更新，mapStateToProps 就会被调用。mapStateToProps 的返回必须是一个对象，这个对象会合并到组件的 props 里。如果不想订阅更新，传一个 null 或者 undefined 即可。  

如果 mapStateToProps 函数被声明了2个参数，第二个参数就是这个被连接的组件的所被传的 props，这两个参数无论谁改变都会被重新执行。

```js
export default connect((state, ownProps) => ({
  commonState: state.common,
  name: state.user.name + ownProps.subName
}))(Signin)
```

##### [mapDispatchToProps(dispatch, [ownProps]): dispatchProps] (Object or Function): 

如果传入一个对象，每个键都会被当成是 action creator。每个 action creator 都会被包裹一个 dispatch，而且会合并到 组件的 props 里，所以可以直接的调用他们而不需要 dispatch。

如果传入一个函数，这个函数的第一个参数将是 dispatch，这将取决于你怎么返回一个对象。一般是用 bindActionCreators() 去处理这个场景。

如果这个函数有2个参数，和上面一样，ownProps 将会是第二个参数。

```js
import { setStatusBar, setRoute } from './actions/common-act'
export default connect((state, haha) => ({
  commonState: state.common,
}), {
  setStatusBar, 
  setRoute,
})(Signin)
```

```js
import * as commonAct from './actions/common-act'
import * as signinAct from './actions/signin-act'
export default connect((state, haha) => ({
  commonState: state.common,
}), (dispatch) => bindActionCreators({ ...commonAct, ...signinAct }, dispatch)
)(Signin)
```

然后都可以在组件里直接调用 this.props.setRoute('common')

##### [mergeProps(stateProps, dispatchProps, ownProps): props] (Function): 

如果指定这个函数，mapStateToProps(), mapDispatchToProps(), 父级 props 将会被当作参数传入。这个函数要返回一个对象，这个对象就会是这个连接组件的 props，所以，组件的 props 完全取决于你在这个函数里返回什么。如果忽略这个函数，将默认使用 Object.assign({}, ownProps, stateProps, dispatchProps) 作为返回。

```js
import * as commonAct from './actions/common-act'
import * as signinAct from './actions/signin-act'
export default connect((state, haha) => ({
  commonState: state.common,
}), (dispatch) => bindActionCreators({ ...commonAct, ...signinAct }, dispatch),
() => ({ foo: 'foo' })
)(Signin)
```

这样，组件的 props 就只有一个 foo 字段