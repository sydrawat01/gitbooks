---
description: Hook to focus and reference custom components.
---

# 🪝 useImperativeHandle() hook

## Problems with `useRef`

`useRef()` cannot be passed to function components. They are only to be used on native HTML elements within the JSX code.

This is where we use a new hook, called `useImperativeHandle` to overcome the above problem.

## `useImperativeHandle`

The `useImperativeHandle` function accepts two parameters.

* First, `ref` , a reserved keyword, to be accepted in the parameter list of the functional component, which is accepted apart from the `props`. This `ref` is used to establish the connection between the component which has the `useRef` hook attached to the native HTML component, and the parent component, where we need to use the reference to that functional component's native HTML component.
* Second, a function, that must return an object. This object must contain all the **data** (variable, function, array, etc) that we will be able to use from outside.

Finally, we need to wrap our functional component with `React.forwardRef()`, which returns a functional component in the end, i.e, our component.

### Syntax

```jsx
const MyComponent = React.forwardRef((props, ref) => {
  //...
  useImperativeHandle(ref, () => {
    return {
      key: value/function
    }
  });
//...
})
```

With this, we can access the `ref` defined the in the native HTML component within our child functional component, in our parent component that uses this child component with the "exposed object data".

So the optimal solution would be:

{% code title="Input.js" %}
```jsx
import React, {useRef, useImperativeHandle} from 'react';

const Input = React.forwardRef((props, ref) => {
  const inputRef = useRef();
  const focusInput = () => {
    inputRef.current.focus();
  }
  useImperativehandler(ref, () => {
    return {
      focus: focusInput
    }
  })
  //...
  //...
  return(
    //...
    <input ref={inputRef} {/*... ...*/} />
  )
})
```
{% endcode %}

{% code title="Login.js" %}
```jsx
import React, {useRef} from 'react';

const Login = () => {
  const emailInputRef = useRef();
  const pwdInputRef = useRef();
  //...
  const formSubmitHandler = (event) => {
    event.preventDefault();
    if(formIsValid) {
      ctx.onLogin(emailState.value, pwdState.value)
    } else if(emailIsInvalid) {
    emailInputRef.current.focus();
      } else if(pwdInputRef) {
    pwdInputRef.current.focus();
    }
  }
  //...
  //...
  return(
    <>
      //...
      <Input ref={emailInputRef} {/*... ...*/} />
      <Input ref={pwdInputRef} {/*... ...*/} />
    </>
  )
}
```
{% endcode %}
