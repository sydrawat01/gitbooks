---
description: Hook to focus JSX elements in React
---

# 🪝 useRef() hook

## Refs

We can use `refs` to `focus` on JSX elements in our React app. `refs` is short for `references`. We use the `ref` keyword on the JSX elements as a property.

There are two ways of using `refs`:

### Class-based components

In the `ref` property, we use an anonymous function:

```jsx
<input 
  key={(inputEl)=>{ this.inputElement = inputEl}} 
  type='text'
  onChange={change}
  defaultValue={name}
/>
```

Here, `inputEl` is a `reference` to the input element where we define the `ref` property. On that element, we then use the element to create a `new prop` called `inputElement`, which is valid since this code is inside the `render() -> return()` , and this will run before `componentDidMount`, and hence the new prop will be added to the component.

In the `componentDidMount` lifecycle hook, we can then add the `focus()` functionality.

```jsx
componentDidMount() {
  this.inputElement.focus();
}
```

This is not the only way to set up references. Since React 16.3, we have another way to set up references, which requires us to use the `constructor()`. We use the `constructor` to initialize and create a reference `prop` on the component.

{% code title="Person.js" %}
```jsx
import React, {Component, createRef} from 'react'
class Person extends Component {
  constructor(props) {
    super(props);
    this.inputElementRef = createRef(); //runs 1st
  }
  componentDidMount() {
    this.inputElementRef.current.focus(); //runs 3rd
  }
  //...
  render() {
    return(
      //...runs 2nd
      <input ref={this.inputElementRef} type="text" onChange={change}>
      //...
    );
  }
}
```
{% endcode %}

When we create the `constructor`, we need to add `super(props)` to it. Next, we create the `reference` to `inputElementRef` as a `prop`.

In the `render` method, we then need to pass `this.inputElementRef` to `ref` property on that JSX element to which we want a reference. Once this is done, in `componentDidMount`, we use this reference to call `focus` on the `current` reference.

### React Hooks (functional components)

We use the `useRef` hook to create a `reference` in functional components.

{% code title="Cockpit.js" %}
```jsx
import React, {useState, useEffect, useRef} from 'react';

const Cockpit = props => {
  const toggleButtonRef = useRef(null); //runs 1st
  useEffect(() => {
    toggleButtonRef.current.click(); // runs 3rd
  }, []);
  //...
  return(
    <div>
      <button
        ref={toggleButtonRef}>
          Toggle Devs
      </button> {/*runs 2nd*/}
    </div>
  );
}
```
{% endcode %}

With `useRef` hook, we create a reference. Next, we assign this reference to the button \[JSX element] where we want to use the toggle functionality. Using the `useEffect` hook to render only once \[i.e, using the empty array as the second parameter to this hook], we call the `click()` method on the `current` reference of `toggleButtonRef`.
