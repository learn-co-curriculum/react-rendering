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

The Circle of Life. Who knew learning about the React framework could bring us
to Lion King? As with every creature in the animal kingdom, React components get
created and destroyed only once. The majority of their lifetime is spent on
updating - that is, reacting to user interactions.

We are kindly provided with 4 lifecycle methods to help us handle
updates:`static getDerivedStateFromProps()`, `shouldComponentUpdate`,
`getSnapshotBeforeUpdate` and `componentDidUpdate`.

These methods always get called in the same order and the `render()` method
which renders the React component into the DOM will be called just before
`componentDidUpdate`, so the actual order of lifecycle methods being called is:

1. `static getDerivedStateFromProps(props, state)`

2. `shouldComponentUpdate(nextProps, nextState)`

4. `render()` (can access props and state via `this.props` and `this.state` -
previous props are no longer available)

5. `getSnapshotBeforeUpdate(prevProps, prevState)`

6. `componentDidUpdate(prevProps, prevState, snapshot)` (can still access
current props and state via `this.props` and `this.state` and this is the last
time previous props and state will be available).

#### `static getDerivedStateFromProps(props, state)`

This method is called when either the component is receiving new props from it's
parent or a the component state has changed, as both cause the component to
update. A word of caution: a common mistake here is to assume that the props
have changed. Just because the method is called doesn't necessarily mean that
the props have changed.

This method is for deriving state, meaning you want to modify a components state
based on something in the new props. React's [official advice][derived] is that
you probably don't need this, and in many cases where it seems necessary, there
is often a better solution.

#### `shouldComponentUpdate(nextProps, nextState)`

The `shouldComponentUpdate` method is the odd one out in the lifecycle methods
as it doesn't operate on the state, but has a `Boolean` return value determining
whether the component should update or not. It's sole use is for custom
performance optimization.

Say for instance, if you only wany a component to update when a value changes
past a set threshold, you could use this method to prevent component updating
_until_ the props meet the requirement.

For general optimization of updating, React recommends an alternative - use
`React.PureComponent` insttead of `React.Component`. From the [React reference
materials][pure]:

> "If your React component’s render() function renders the same result given the
same props and state, you can use React.PureComponent for a performance boost in
some cases."

The `PureComponent` does not have access to `shouldComponentUpdate`, because it
instead runs its own version. The `PureComponent` checks to see if there are any
_shallow_ changes to props and state and will only update if it  registers a
difference between the current and next states.

When you applications start to get really big, using `PureComponent` is useful
for stopping un-necessary re-renders and making your website faster. When you
really want to customize the logic for when to re-render, use
`shouldComponentUpdate`.

```javascript
shouldComponentUpdate(nextProps, nextState) {
  return (this.props.myImportantValue !== nextProps.myImportantValue);
}
```

#### `render()`

The `render()` method is the most familiar one to all React developers. In fact,
in everyday development, we often end up writing React components that only use
the `render()` method! At this stage, the next props and state have become
available from `this.props` and `this.state` and the component gets rendered
into the DOM.

In a theater, this is when a specific scene in a play is being carried out: no
changes are being made to the script or the scenery at this stage, it is purely
taking the script the director is happy with and the props that have been laid
out, and carrying out the play.

#### `getSnapshotBeforeUpdate(prevProps, prevState)`

Right after render, but just before React commits content to the DOM in an
update, the `getSnapshotBeforeUpdate` method is fired. This method is currently
only used to capture information that may be changed after an update. For
instance, mouse position and scroll position might be changing rapidly and will
change by the time the next lifecycle method is invoked. This method returns
either `null` or a value that will be passed into the next method,
`componentDidUpdate`.

#### `componentDidUpdate(prevProps, prevState, snapshot)`

This method isn't used very often, but it is kind of a look back to the update
that just occurred. We will have access to both the current props and previous
props, as well as any snapshot info from `getSnapshotBeforeUpdate`. A common use
case for this would be to update a 3rd party library.

```javascript
  componentDidUpdate(prevProps, prevState) {
   if (prevProps.height !== this.props.height) {
     someChartLibrary.updateHeight(this.props.height);
   }
  }
```

This method can also be used to interact with the DOM, say be adjusting scroll
position.

## Summary

These are all the tools we get to help us decide how to react to changes in our
component. The `render()` method is the only one we *must* have - everything
else is optional. Use these methods sparingly. In the ideal world, we want to
have as little of these methods as possible.

### Updating lifecycle methods

Not called on initial render, but always called whenever a subsequent re-render is triggered:

| Method            | current props and state | prevProps | prevState | nextProps |  nextState | Can call `this.setState` | Called when?               | Used for                                                                                    |
|:-------------------------:|:---------:|:---------:|:----------------------:|:-------------------------------------------------------:|:--------------------------------------------------------------------------------:|
| `static getDerivedStateFromProps()` |    yes    |     no    |     no    |     no    |     no    |     yes     |     before every render  |   Not used often |
|   `shouldComponentUpdate`   |    yes    |    no    |    no    |    yes    |    yes   |    yes    | before every re-render (not initially) | can be used to stop unnecessary re-renders for performance optimization |
|     `getSnapshotBeforeUpdate`    |    yes   |    yes   |    yes   |    no   |    no   |    yes   | just before React updates and commits new content to the DOM | used rarely; can capture data that may be changing rapidly |
|     `componentDidUpdate`    |    yes   |    yes    |    yes    |    no   |    no    |    yes    | just after a re-render has finished | any DOM updates following a render (mostly interacting with 3rd party libraries) |

Current props and state are always
\* `componentDidUpdate` will actually receive `prevProps` and `prevState` as arguments, as the newly applied state and props can be accessed through `this.props` and `this.state`.

<p align="center">
  <img src="https://media.giphy.com/media/wDOFUCaxyv2XC/giphy.gif" />
</p>

## Resources

- [React: Component Specs and Lifecycle](https://github.com/learn-co-curriculum/react-rendering)
- [Idempotent Operations](https://stackoverflow.com/questions/1077412/what-is-an-idempotent-operation)

<p class='util--hide'>View <a href='https://learn.co/lessons/react-rendering'>Rendering</a> on Learn.co and start learning to code for free.</p>

[derived]: https://reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html
[pure]: https://reactjs.org/docs/react-api.html#reactpurecomponent
