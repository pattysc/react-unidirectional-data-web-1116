# React Unidirectional Data

## Objectives

1. Explain what we mean by "unidirectional data"
2. Explain how React makes use of unidirectional data in components (think
   `setState()`)
3. Describe how to take advantage of unidirectional data flow in an application

## Overview

We can now get students thinking outside of the component box and in terms of
somewhat larger applications. We can move from simply having handlers outside
of our components to keeping state outside of components as well. We're not
quite ready for full-blown flux, but we at least have the concepts of actions
and stores, and we can already make use of them:

```javascript
// stores/Store.js
module.exports = class Store {
  constructor(state) {
    this.listeners = []
    this.state = state
  }

  addListener(l) {
    this.listeners.push(l)

    return this.listeners.length - 1
  }

  removeListener(id) {
    this.listeners = [
      ...this.listeners.slice(0, id),
      ...this.listeners.slice(id + 1)
    ]
  }

  updateState(state) {
    this.state = state

    this.listeners.forEach(l => l())
  }
}

// stores/togglerStore.js
const Store = require('./Store')

const initialState = {
  on: false
}

module.exports = new Store(initialState)

// actions/togglerActions.js
const togglerStore = require('../stores/togglerStore')

function handleClick(e) {
  e.preventDefault()

  togglerStore.updateState({ on: !togglerStore.state.on })
}

// components/Toggler.js
const React = require('react')

const togglerActions = require('../actions/togglerActions')
const togglerStore = require('../stores/togglerStore')

module.exports = class Toggler extends React.Component {
  constructor(props) {
    super(props)

    this.handleClick = togglerActions.handleClick.bind(this)
    this.updateState = this.updateState.bind(this)

    this.state = togglerStore.state
  }

  componentDidMount() {
    togglerStore.addListener(this.updateState)
  }

  render() {
    return <a onClick={this.handleClick}>{this.state.on}</a>
  }

  updateState(state) {
    // we can also perform other transformations here
    this.setState(state)
  }
}
```

This is an admittedly contrived example, but it lets us bypass the dispatcher
(for now) and demonstrates the data flow in what is hopefully a reasonably
obvious way.

## Resources

- [Redux](http://redux.js.org/)
- [Flux](https://facebook.github.io/flux/docs/overview.html)
