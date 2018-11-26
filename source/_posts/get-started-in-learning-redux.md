---
title: get-started-in-learning-redux
date: 2018-11-21 14:33:07
tags:
---
# Learn Redux
[link](https://redux.js.org)
## Motivation
- manage complicated state
- to handle mutation & asynchronicity for your data

## Core Concepts
- app's state
- action
- reducer(connect action and app's state)

## Three Princples
- Single source of true
- State is read-only
- Changes are made with pure functions
## Basic Tutorial
### Actions
About define action type constants in seperate file: [Note on Boilerplate](https://redux.js.org/recipes/reducingboilerplate)

> It's a good idea to pass as little data in each action as possible. For example, it's better to pass index than the whole todo object.

- `dispatch()` can be accessed from the store, but more likely you'll access it using a helper like `react-redux`'s `connect()`
- Action creators can also be asynchronous and have side-effects.
### Reducers
#### Designing the state shape
#### Handling actions
It's called a reducer because it's the type of function you would pass to `Array.prototype.reduce(reducer, ?initialValue)`

We'll explore how to perform side effects in the advanced walkthrough. For now, just remember that the reducer must be pure. Given the same arguments, it should calculate the next state and return it. No surprises. No side effects. No API calls. No mutations. Just a calculation.
#### Handling More Actions
[Immutable](https://facebook.github.io/immutable-js/)
#### Spliting Reducers
Code so far:
```Javascript
function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return Object.assign({}, state, {
        visibilityFilter: action.filter
      })
    case ADD_TODO:
      return Object.assign({}, state, {
        todos: [
          ...state.todos,
          {
            text: action.text,
            completed: false
          }
        ]
      })
    case TOGGLE_TODO:
      return Object.assign({}, state, {
        todos: state.todos.map((todo, index) => {
          if (index === action.index) {
            return Object.assign({}, todo, {
              completed: !todo.completed
            })
          }
          return todo
        })
      })
    default:
      return state
  }
}
```
Try to make it easier to comprehend:
```Javascript
function todos(state = [], action) {
  switch (action.type) {
    case ADD_TODO:
      return [
        ...state,
        {
          text: action.text,
          completed: false
        }
      ]
    case TOGGLE_TODO:
      return state.map((todo, index) => {
        if (index === action.index) {
          return Object.assign({}, todo, {
            completed: !todo.completed
          })
        }
        return todo
      })
    default:
      return state
  }
}
function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return Object.assign({}, state, {
        visibilityFilter: action.filter
      })
    case ADD_TODO:
      return Object.assign({}, state, {
        todos: todos(state.todos, action)
      })
    case TOGGLE_TODO:
      return Object.assign({}, state, {
        todos: todos(state.todos, action)
      })
    default:
      return state
  }
}

```
`todoApp` just give its slice of the state to `todos`, and `todos`knows how to update just that slice.
> This is called reducer composition, and it's the fundamental pattern of building Redux apps.

use `combineReducers` to simplify the code
```Javascript
export default function todoApp(state = {}, action) {
  return {
    visibilityFilter: visibilityFilter(state.visibilityFilter, action),
    todos: todos(state.todos, action)
  }
}
```
vs
```Javascript
import { combineReducers } from 'redux'

const todoApp = combineReducers({
  visibilityFilter,
  todos
})
export default todoApp
```
### Store
The responsibility of the `Store`
- hold application state
- allow access to state via `getState`
- allow state to be updated via `dispatch(action)`
- register listeners via `subscribe(listener)`
- handles unregistering of listeners via the function returned by `subscribe(listener)` (i.e you can unscribe the register by that returned value)

### Data Flow
> Redux architecture revolves around a strict unidirectional data flow.
- You call store.dispatch(action)
- The Redux store calls the reducer function you gave it.
- The root reducer may combine the output of multiple reducers into a single state tree.
- The Redux store saves the complete state tree returned by the root reducer
- Every listener registered with store.subscribe(listener) will now be invoked
### Usage with React
Presentational and Container Components
> Technically you could write the container components by hand using store.subscribe(). We don't advise you to do this because React Redux makes many performance optimizations that are hard to do by hand. For this reason, rather than write container components, we will generate them using the connect() function provided by React Redux, as you will see below.

- Designing Component Hierarchy
- Designing Container Components
- Designing Other Components

#### Inplementing Components
- inplementing presentational components
- inplementing container components(use React Redux Lib's `connect()`, which provides many useful optimization)
	- `mapStateToProps()`
	- `mapDispatchToProps`

#### Passing the Store
use `<Provider>`

## Advanced
### Async Actions
#### Actions

Usually, for any API request you'll want to dispatch at least three different kinds of actions:
- An action informing the reducers that the request began.
- An action informing the reducers that the request finished successfully.
- An action informing the reducers that the request failed.

#### Synchronous Action Creators

#### Designing the State Shape
> Note on Nested Entities

#### Handling Actions
write reducers
#### Async Action Creators
use `redux-thunk`:
by using this specific middleware, an action creator can return a function instead of an action object.

### Async Flow
use middleware
- `redux-thunk`: plain object -> function
- `redux-promise`: plain object -> Promise
### Middleware
#### Understanding Middleware
Problem:logging
1. log mannually
2. wrapping dispatch
3. mokeypatching dispatch

Problem:crash reporting
4. hiding monkeypatching
5. Naively Applying the Middleware
6. Final Approach

#### Seven examples
[link](https://redux.js.org/advanced/middleware)

### Usage with React Router

