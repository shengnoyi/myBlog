---
title: typescript-react-starter
date: 2018-11-19 16:37:17
tags:
---
## Creating a component

```Javascript
// src/components/Hello.tsx

import * as React from 'react';

export interface Props {
  name: string;
  enthusiasmLevel?: number;
}

function Hello({ name, enthusiasmLevel = 1 }: Props) {
  if (enthusiasmLevel <= 0) {
    throw new Error('You could be a little more enthusiastic. :D');
  }

  return (
    <div className="hello">
      <div className="greeting">
        Hello {name + getExclamationMarks(enthusiasmLevel)}
      </div>
    </div>
  );
}

export default Hello;

// helpers

function getExclamationMarks(numChars: number) {
  return Array(numChars + 1).join('!');
}
```
>writing an SFC makes more sense here, but it's important to know how to write a class component.
```Javascript
class Hello extends React.Component<Props, object> {
  render() {
    const { name, enthusiasmLevel = 1 } = this.props;

    if (enthusiasmLevel <= 0) {
      throw new Error('You could be a little more enthusiastic. :D');
    }

    return (
      <div className="hello">
        <div className="greeting">
          Hello {name + getExclamationMarks(enthusiasmLevel)}
        </div>
      </div>
    );
  }
}
```

```Javascript
import React from 'react'
import ReactDOM from 'react-dom'
import Hello from './components/Hello'

ReactDOM.render(<Hello name="TypeScript" enthusiasmLevel={10}/>, document.getElementById('root') as HTMLElement)
```

## Type assertions
```Javascript
document.getElementById('root') as HTMLElement
```
>This syntax is called a type assertion, sometimes also called a cast. This is a useful way of telling TypeScript what the real type of an expression is *when you know better* than the type checker.

## Statefull components
>Deeply understanding best practices around component state in React are out of the scope of this starter, but let's quickly peek at a stateful version of our Hello component to see what adding state looks like. We're going to render two <button>s which update the number of exclamation marks that a Hello component displays.

To do that, we're going to
1. Define a type for our state (i.e. this.state)
2. Initialize this.state based on the props we're given in our constructor.
3. Create two event handlers for our buttons (onIncrement and onDecrement).

```Javascript
// src/components/StatefulHello.tsx

import * as React from "react";

export interface Props {
  name: string;
  enthusiasmLevel?: number;
}

interface State {
  currentEnthusiasm: number;
}

class Hello extends React.Component<Props, State> {
  constructor(props: Props) {
    super(props);
    this.state = { currentEnthusiasm: props.enthusiasmLevel || 1 };
  }

  onIncrement = () => this.updateEnthusiasm(this.state.currentEnthusiasm + 1);
  onDecrement = () => this.updateEnthusiasm(this.state.currentEnthusiasm - 1);

  render() {
    const { name } = this.props;

    if (this.state.currentEnthusiasm <= 0) {
      throw new Error('You could be a little more enthusiastic. :D');
    }

    return (
      <div className="hello">
        <div className="greeting">
          Hello {name + getExclamationMarks(this.state.currentEnthusiasm)}
        </div>
        <button onClick={this.onDecrement}>-</button>
        <button onClick={this.onIncrement}>+</button>
      </div>
    );
  }

  updateEnthusiasm(currentEnthusiasm: number) {
    this.setState({ currentEnthusiasm });
  }
}

export default Hello;

function getExclamationMarks(numChars: number) {
  return Array(numChars + 1).join('!');
}
```
# Adding state management
Use Redux to add state management for the application
## State management in general
## Setting the stage for actions
## Installing Redux
## Defining out app's state
## Adding actions
## Adding a reducer
## Making container
- `mapStateToProps`:which massages the data from the current store to part of the shape that our component needs.
- `mapDispatchToProps`:which creates callback props to pump actions to our store using a given `dispatch` function.
## Creating a store
