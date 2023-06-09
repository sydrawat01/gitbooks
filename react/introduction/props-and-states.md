---
description: >-
  State and Props are CORE concepts of React. Actually, only changes in and/ or
  state trigger React to re- render your components and potentially update the
  DOM in the browser.
---

# 🏗 Props & States

## Props

`props` allow you to pass data from parent(wrapping) component to a child(embedded) component.

### `AllPosts` Component:

```jsx
const posts = () => {
  return (
    <div>
      <Post title="My first Post" />
    </div>
  );
}
```

Here, `title` is the custom property(`prop`) set up on the custom `Post` component. We basically replicate the default HTML attribute behavior we already know.

### `Post` Component

```jsx
const post = () => {
  return(
    <div>
      <h1>{props.title}</h1>
    </div>
  );
}
```

The `Post` component receives the `props` argument. You can of course name this argument whatever you want - it's your function definition. But React will pass one argument to your component function - an object, which contains all properties you set up on `<Post ... />`.

`{props.title}` then dynamically outputs the `title` property of the `props` object - which is available since we set the `title` property inside `AllPosts` component.

## State

Whilst props allow you to pass data down the component tree (and hence trigger an UI update), state is used to change the component, well, state from within. Changes to state also trigger an UI update.

### `NewPost` Component:

```jsx
class NewPost extends Component {
  // state can only be accessed in class-based components!
  state = {
    counter: 1
  };

  render() {
    //needs to be implemented in class-based components!
    //Needs to return some JSX!
    return(
      <div>{this.state.counter}</div>
    );
  }
}
```

Here, the `NewPost` component contains the `state`. Only class-based components can define and use `state`. You can of course pass the `state` down to functional components, but these then can't directly edit it.

`state` simply has a property of the component class, you have to call it `state` though - the name is not optional. You can then access it via `this.state` in your class `JSX` code (which you return in the required`render()` method).

Whenever `state` changes, the component will re-render and reflect the new state. The difference to `props` is, that this happens with one and the same component - you don't receive new data(`props`) from outside!

## Setting `state` correctly

Sometimes, using `this.state` does not guarantee that it will return the current state.

This is the wrong way to declare state:

```jsx
state = { counter: 0 }

changeHandler = () => {
  //...
  this.setState({counter: this.state.counter + 1});
}
```

The correct way to use state is using an anonymous function within `setState`, which takes two parameters:

```jsx
state = {counter: 0}

this.setState((prevState, props) => {
  return {
    counter: prevState.counter + 1;
  };
});
```

The first parameter `prevState` is the previous state of the `this.state` and the second parameter is the `props` of the React component.
