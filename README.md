# React Rendering

## Objectives

1. Explain what happens when React renders a component
2. Explain why `render()` must be idempotent
3. Describe the parts of the component lifecycle leading up to and coming after
   `render()`

## Overview

Like the deep dive on mounting and unmounting, we want to giving students a
chance to really sink their teeth into the render (or update) part of the React
component lifecycle.

It's important to home in on idempotency as well as the right time to perform
certain actions (e.g., DOM updates go in `componentDidUpdate()`).

Of special concern might be `shouldComponentUpdate()`. This is a handy method
for components that just seem to want to do too much. It can also be confusing,
I think, because it's this weird boolean escape hatch from the rest of the
update portion of the lifecycle.

## Resources

- [React: Component Specs and Lifecycle](https://github.com/learn-co-curriculum/react-rendering)
