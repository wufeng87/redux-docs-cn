原文链接： 

https://github.com/reactjs/redux/blob/master/docs/basics/Actions.md

或者

http://redux.js.org/docs/basics/Actions.html

# Actions

首先，让我们定义一些actions。

**Actions**是一种数据载体，用于从应用向你的store发送数据。Actions是你的store*唯一*的数据来源。你需要使用[`store.dispatch()`](../api/Store.md#dispatch)将Action发送到store

下面是一个action的例子，这个action的含义为：新增一个todo项

```js
const ADD_TODO = 'ADD_TODO'
```

```js
{
  type: ADD_TODO,
  text: 'Build my first Redux app'
}
```

Actions都是普通JavaScript对象。Actions必须有一个`type`属性，type属性用于指定你的action的类型。types一般都需要被定义为字符串常量。一旦你的应用变得足够大，你可能需要将这些type常量移动到独立的子模块中：

```js
import { ADD_TODO, REMOVE_TODO } from '../actionTypes'
```

>##### Note on Boilerplate

>你不是一定要在单独的文件中定义action type常量，甚至连定义都可以省去。对于一个小项目，可能直接使用字符串字面量的action type更为简单。当然，将action type显示的声明为字符串常量会带来很多好处，尤其是在大的项目中。请移步阅读[Reducing Boilerplate](../recipes/ReducingBoilerplate.md)来获得更多的能使你的代码库简洁的建设性意见。

除了`type`属性，action对象的其他数据结构都可以由你自己定义。如果感兴趣，想了解actions的数据结构应当怎么样被组织，可以看看[Flux Standard Action](https://github.com/acdlite/flux-standard-action)。

Other than `type`, the structure of an action object is really up to you. If you're interested, check out [Flux Standard Action](https://github.com/acdlite/flux-standard-action) for recommendations on how actions could be constructed.

接下来，我们将创建一个新的action type，用于描述用户将一个todo项设置为完成状态的动作。因为我们的store简单的将所有todo项存储在一个数组中了，所以这里我们使用了`index`变量来代表一个todo项。在真实的项目中，我们一般不这么做，而是使用一个唯一的ID来标识一个新对象。

We'll add one more action type to describe a user ticking off a todo as completed. We refer to a particular todo by `index` because we store them in an array. In a real app, it is wiser to generate a unique ID every time something new is created.

```js
{
  type: TOGGLE_TODO,
  index: 5
}
```

需要注意是，我们要尽可能简洁的组织一个action的数据结构。比如这里，我们使用了`index`，而不是用的一整个todo项对象。

It's a good idea to pass as little data in each action as possible. For example, it's better to pass `index` than the whole todo object.

最后，我们再新建一个新的action type，用于改变目前所有的todo项的可见性（是否显示在页面中）。

Finally, we'll add one more action type for changing the currently visible todos.

```js
{
  type: SET_VISIBILITY_FILTER,
  filter: SHOW_COMPLETED
}
```

## Action Creators

**Action creators**是用于创建action的工厂类。Action和Action creators是两个容易混淆的概念。

**Action creators** are exactly that—functions that create actions. It's easy to conflate the terms “action” and “action creator,” so do your best to use the proper term.

在Redux中，action creator简单的返回一个action：

In Redux action creators simply return an action:

```js
function addTodo(text) {
  return {
    type: ADD_TODO,
    text
  }
}
```

这样使得他们更为便携，且易于测试。

This makes them portable and easy to test.

在 [traditional Flux](http://facebook.github.io/flux)中，action creators被调用的时候经常会触发一个dispatch（action派发），例如：

In [traditional Flux](http://facebook.github.io/flux), action creators often trigger a dispatch when invoked, like so:

```js
function addTodoWithDispatch(text) {
  const action = {
    type: ADD_TODO,
    text
  }
  dispatch(action)
}
```

这*并不是*Redux中的使用方式。在Redux中，我们会将新创建的action对象作为参数传给`dispatch()`方法：

In Redux this is *not* the case.  
Instead, to actually initiate a dispatch, pass the result to the `dispatch()` function:

```js
dispatch(addTodo(text))
dispatch(completeTodo(index))
```

当然，你也可以像下面这样，创建一个**bound action creator**来自动的创建action并派发action：

Alternatively, you can create a **bound action creator** that automatically dispatches:

```js
const boundAddTodo = (text) => dispatch(addTodo(text))
const boundCompleteTodo = (index) => dispatch(completeTodo(index))
```

然后，你可以像这样去方便的使用它们：

Now you'll be able to call them directly:

```
boundAddTodo(text)
boundCompleteTodo(index)
```

`dispatch()`方法可以使用[`store.dispatch()`](../api/Store.md#dispatch)的方式直接调用，但你大多数情况下都会需要[react-redux](http://github.com/gaearon/react-redux)'s `connect()`这样的的工具类。为了能自动的批量绑定action creators，你需要使用[`bindActionCreators()`](../api/bindActionCreators.md)。

The `dispatch()` function can be accessed directly from the store as [`store.dispatch()`](../api/Store.md#dispatch), but more likely you'll access it using a helper like [react-redux](http://github.com/gaearon/react-redux)'s `connect()`. You can use [`bindActionCreators()`](../api/bindActionCreators.md) to automatically bind many action creators to a `dispatch()` function.

Action creators也可以是异步且有副作用的。在
[advanced tutorial](../advanced/README.md) 目录下的[async actions](../advanced/AsyncActions.md)文档，介绍了如何处理AJAX响应，以及如何将action creators整合到异步控制流程中。请先阅读完所有基础教程后再去阅读advanced tutorial。

Action creators can also be asynchronous and have side-effects. You can read about [async actions](../advanced/AsyncActions.md) in the [advanced tutorial](../advanced/README.md) to learn how to handle AJAX responses and compose action creators into async control flow. Don't skip ahead to async actions until you've completed the basics tutorial, as it covers other important concepts that are prerequisite for the advanced tutorial and async actions.

## Source Code

### `actions.js`

```js
/*
 * action types
 */

export const ADD_TODO = 'ADD_TODO'
export const TOGGLE_TODO = 'TOGGLE_TODO'
export const SET_VISIBILITY_FILTER = 'SET_VISIBILITY_FILTER'

/*
 * other constants
 */

export const VisibilityFilters = {
  SHOW_ALL: 'SHOW_ALL',
  SHOW_COMPLETED: 'SHOW_COMPLETED',
  SHOW_ACTIVE: 'SHOW_ACTIVE'
}

/*
 * action creators
 */

export function addTodo(text) {
  return { type: ADD_TODO, text }
}

export function toggleTodo(index) {
  return { type: TOGGLE_TODO, index }
}

export function setVisibilityFilter(filter) {
  return { type: SET_VISIBILITY_FILTER, filter }
}
```

## Next Steps

下一步我们来一起[定义一些 reducers](Reducers.md)，去看看当action被派发(dispatch)后是如何更新state的。

Now let's [define some reducers](Reducers.md) to specify how the state updates when you dispatch these actions!

