# React Rendering

## Objectives

1. Explain what happens when React renders a component
2. Explain why `render()` must be idempotent
3. Describe the parts of the component lifecycle leading up to and coming after
   `render()`

## Overview

<p align="center">
  <img src="http://www.awesomelyluvvie.com/wp-content/uploads/2014/04/lion-king-circle-of-life.gif" />
</p>

The Circle of Life. Who knew learning about the React framework could bring us to Lion King? As with every creature in
the in the animal kingdom, React components get created and destroyed only once. Most of the time they spend
updating - that is, reacting to our interactions.

We are kindly provided with 4 lifecycle methods to help us handle updates:`componentWillReceiveProps`,
`shouldComponentUpdate`, `componentWillUpdate` and `componentDidUpdate`.

These methods always get called in the same order and the `render()` method which renders the React component into
the DOM will be called just before `componentDidUpdate`, so the actual order of lifecycle methods being called is:

1. `componentWillReceiveProps(nextProps)`

2. `shouldComponentUpdate(nextProps, nextState)`

3. `componentWillUpdate(nextProps, nextState)`

4. `render()` (can access props and state via `this.props` and `this.state` - previous props are no longer available)

5. `componentDidUpdate(prevProps, prevState)` (can still access current props and state via `this.props` and
`this.state` and this is the last time previous props and state will be available as they are passed into the function).

### `componentWillReceiveProps(nextProps)`
This method is called when the component is receiving new props from it's parent. A word of caution: a common mistake
here is to assume that the props have changed. Just because the method is called doesn't necessarily mean that the props
have changed.

### `shouldComponentUpdate(nextProps, nextState)`
`shouldComponentUpdate` is the odd one out in the lifecycle methods as it doesn't operate on the state, but has a
`Boolean` return value determining whether the component should update or not. It's useful to prevent un-necessary
re-renders and making your website faster (this is useful especially when your application gets really big!).

```javascript
shouldComponentUpdate(nextProps, nextState) {
  return (this.props.myImportantValue !== nextProps.myImportantValue);
}
```

For example, the above code means that the React component gets re-rendered when `myImportantValue` has changed. A word
of caution though: you might think it'd be a good idea to use the `shouldComponentUpdate` function to only re-render the
component if *any* of the props have changed and avoid *all* redundant re-renders, e.g. if `this.props !== nextProps`.
However, because `props` and `nextProps` are both JavaScript objects, this comparison will always return `true`, that
is `{} === {}` is never `true` in JavaScript (object equality is one of the many, many JavaScript quirks out there...
The reasons behind it are a bit too advanced to explain at this stage, it's enough just to know about it. Further
reading [here](http://adripofjavascript.com/blog/drips/object-equality-in-javascript.html). 

### `componentWillUpdate(nextProps, nextState)`
**componentWillUpdate** is called immediately after the check in **shouldComponentUpdate** has passed. No state changes
are allowed in this method and it should be used solely for preparing for the upcoming update, not trigger one. One of
the more common uses of **componentWillUpdate** is to to call an action, set a variable or start an animation (not in
the state) based on state changes.

### `render()`
The `render()` method is the most familiar one to all React developers. In fact, in everyday development, we often end
up writing React components that only use the `render()` method! At this stage, the next props and state have become
available from `this.props` and `this.state` and the component gets rendered into the DOM.

### `componentDidUpdate(prevProps, prevState)`
This method used very often, but it is a kind of a looking back to the opdate that's just occurred. We will have access
to both the current props and previous props. A common use case for this would be to update a 3rd party library.

```javascript
  componentDidUpdate(prevProps, prevState) {
   if (prevProps.height !== this.props.height) {
     someChartLibrary.updateHeight(this.props.height);
   }
  }
```

## Summary
These are all the tools we get to help us decide how to react to changes in our component. The `render()` method is the
only one we *must* have - everything else is optional. Use these methods sparingly. In the ideal world, we want to have
as little of them methods as possible.

### Updating lifecycle methods
Not called on initial render, but always called whenever a subsequent re-render is triggered:

|           Method          | nextProps | nextState | Can call `this.setState` |                       Called when?                      |                                     Used for                                     |
|:-------------------------:|:---------:|:---------:|:----------------------:|:-------------------------------------------------------:|:--------------------------------------------------------------------------------:|
| `componentWillReceiveProps` |    yes    |     no    |           yes          |  many times, whenever component is going to receive new props  |                     applying state changes based on new props                    |
|   `shouldComponentUpdate`   |    yes    |    yes    |           no           |    many times, whenever a re-render has been triggered    |    deciding based on new & old props & state whether a re-render should occur    |
|    `componentWillUpdate`    |    yes    |    yes    |           no           | many times, when new state and props are being received | prepare for the update, dispatch any actions or animations based on state change |
|     `componentDidUpdate`    |    yes*   |    yes*   |           yes          |    many times, just after the re-render has finished    | any DOM updates following a render (mostly interacting with 3rd party libraries) |

\* `componentDidUpdate` will actually receive `prevProps` and `prevState` as arguments, as the newly applied state and props can be accessed through `this.props` and `this.state`.

<p align="center">
  <img src="https://media.giphy.com/media/wDOFUCaxyv2XC/giphy.gif" />
</p>

## Resources

- [React: Component Specs and Lifecycle](https://github.com/learn-co-curriculum/react-rendering)
