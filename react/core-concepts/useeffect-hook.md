---
description: Hook for side-effects.
---

# 🪝 useEffect() hook

## `useEffect` and "side effects"

![Normal Tasks v/s Side Effects.](<../.gitbook/assets/Screenshot 2021-05-19 at 16.17.49.png>)

## Working with `useEffect()`&#x20;

### Syntax

```jsx
useEffect( ()=> {...}, [ dependencies ]);
```

![useEffect() hook syntax breakdown.](<../.gitbook/assets/Screenshot 2021-05-19 at 16.24.32.png>)

The `useEffect()` hook takes in two parameters:

* First is a function, which needs to be executed AFTER every component evaluation IF the specified dependencies change.
* Second is an array of dependencies of the effect, so that the function in the first parameter only runs IF the dependencies mentioned in this array change.

Consider the following scenario:

```jsx
const App = () => {
  const [isLoggedIn, setIsLoggedIn] = useState(false)
  const userLogInfo = localStorage.getItem('isLoggedIn');
  if( userLogInfo === 'LOGGED_IN') {
    setLoggedIn(true);
  }
  //...
  //...
  const loginHandler = (email, pwd) => {
    //some check for matching email and pwd at backend
    localStorage.setItem('isLoggedIn', 'LOGGED_IN')
    setIsLoggedIn(true);
  }
}
```

Since we login into this app, the `localStorage` is persistent, and will store the `isLoggedIn` value as `LOGGED_IN` even if the page is refreshed. So every time we check the `localStorage` item (highlighted in yellow above), we call the `setIsLoggedIn` `useState` hook function to set the state.

When we call a state setting function, the `setIsLoggedIn` function here, the component function, i.e, the `App` component function, re-executes, and therefore the `if` statement re-executes, and causes another state setting function call (`setLoggedIn`), and so on, continuously, in an infinite loop.

This is where `useEffect` comes in handy, to control when we need to set the state in the functional component, and trigger a re-render.

```jsx
import React, {useState, useEffect} from 'react';

const App = () => {
  const [isLoggedIn, setIsLoggedIn] = useState(false);

  useEffect(() => {
    const userLoginInfo = localStorage.getItem('isLoggedIn')
    if( userLoginInfo === 'LOGGED_IN' ) {
      setIsLoggedIn(true);
    }
  }, [])
}
```

Now, since we added the `setIsLoggedIn` state based on the `localStorage` info WITHIN the `useEffect` first parameter, this anonymous function will run only AFTER all the components are evaluated, and so will set the state after the first render. Eventually the anonymous function in the first parameter will only run again, if the dependencies in the second parameter have changed.

We do not have any dependencies, since the second parameter in the `useEffect` hook is an empty array. So, the anonymous function in the first parameter will run when the app is loading for the first time, since there were no dependencies to begin with, and now we have an empty array of dependencies, which do not change. So the anonymous function in the `useEffect` hook only runs once, i.e, when the app starts up for the first time.

We are basically reducing the data intensive task, i.e, fetching the login details from the local storage. We are fetching the data from the `localStorage` only when the dependencies in the `useEffect` change, and not on every re-render of the component. Since we selectively fetch the login info, we then only selectively update the state of `isLoggedIn` state variable. This prevents an infinite loop of re-rendering every time we call the `setIsLoggedIn` state setting function and also continuous data fetching from the `localStorage`\[which in the real case scenario, would be fetched from a server, and would be more intensive task affecting app performance].

### Dependencies

Consider the following `Login` component:

{% code title="Login.js" %}
```jsx
import React, { useState } from 'react';
import Card from '../UI/Card/Card';
import classes from './Login.module.css';
import Button from '../UI/Button/Button';

const Login = (props) => {
  const [enteredEmail, setEnteredEmail] = useState('');
  const [emailIsValid, setEmailIsValid] = useState();
  const [enteredPassword, setEnteredPassword] = useState('');
  const [passwordIsValid, setPasswordIsValid] = useState();
  const [formIsValid, setFormIsValid] = useState(false);

  const emailChangeHandler = (event) => {
    setEnteredEmail(event.target.value);

    setFormIsValid(
      event.target.value.includes('@') && enteredPassword.trim().length > 6
    );
  };

  const passwordChangeHandler = (event) => {
    setEnteredPassword(event.target.value);

    setFormIsValid(
      event.target.value.trim().length > 6 && enteredEmail.includes('@')
    );
  };

  const validateEmailHandler = () => {
    setEmailIsValid(enteredEmail.includes('@'));
  };

  const validatePasswordHandler = () => {
    setPasswordIsValid(enteredPassword.trim().length > 6);
  };

  const submitHandler = (event) => {
    event.preventDefault();
    props.onLogin(enteredEmail, enteredPassword);
  };

  return (
    <Card className={classes.login}>
      <form onSubmit={submitHandler}>
        <div
          className={`${classes.control} ${
            emailIsValid === false ? classes.invalid : ''
          }`}
        >
          <label htmlFor="email">E-Mail</label>
          <input
            type="email"
            id="email"
            value={enteredEmail}
            onChange={emailChangeHandler}
            onBlur={validateEmailHandler}
          />
        </div>
        <div
          className={`${classes.control} ${
            passwordIsValid === false ? classes.invalid : ''
          }`}
        >
          <label htmlFor="password">Password</label>
          <input
            type="password"
            id="password"
            value={enteredPassword}
            onChange={passwordChangeHandler}
            onBlur={validatePasswordHandler}
          />
        </div>
        <div className={classes.actions}>
          <Button type="submit" className={classes.btn} disabled={!formIsValid}>
            Login
          </Button>
        </div>
      </form>
    </Card>
  );
};

export default Login;
```
{% endcode %}

In the above code, we can see that the `setFormIsValid` state setting function is being called twice, and is mostly redundant. We can remove this to use `useEffect` hook to only validate the form inputs based on the input values and length.

```jsx
import React, {useState, useEffect} from 'react'

const Login = (props) => {
  const [enteredEmail, setEnteredEmail] = useState('');
  const [emailIsValid, setEmailIsValid] = useState();
  const [enteredPassword, setEnteredPassword] = useState('');
  const [passwordIsValid, setPasswordIsValid] = useState();
  const [formIsValid, setFormIsValid] = useState(false);

  useEffect(()=>{
    setFormIsValid(enteredEmail.includes('@' && enteredPassword.trim().length > 6)
  },[ setFormIsValid, enteredEmail, enteredPassword ])
}
```

In the dependencies, we add the `setFormIsValid`, `enteredEmail` and `enteredPassword` to the array, since we need to run the `useEffect` hook's anonymous function, which sets the form validation only when the values to the three dependencies change.

**NOTE:** We can omit `setFormValid` here since it is a function of `useState`, which is ensured by React that it will never change.

```jsx
useEffect( ()=> {
  setFormIsValid(enteredEmail.includes('@') && enteredPassword.trim().length > 6)
}, [ enteredEmail, enteredPassword ])
```

This comes in handy typically **when we need to re-run some logic when some data or state changes**.

### To add or not to add!

In the previous lecture, we explored `useEffect()` dependencies.

You learned, that you should add "everything" you use in the effect function as a dependency - i.e. all state variables and functions you use in there.

That is correct, but there are a **few exceptions** you should be aware of:

* You **DON'T need to add state updating functions** (as we did in the last lecture with`setFormIsValid`): React guarantees that those functions never change, hence you don't need to add them as dependencies (you could though)
* You also **DON'T need to add "built-in" APIs or functions** like `fetch()`, `localStorage`etc (functions and features built-into the browser and hence available globally): These browser APIs / global functions are not related to the React component render cycle and they also never change
* You also **DON'T need to add variables or functions** you might've **defined OUTSIDE of your components** (e.g. if you create a new helper function in a separate file): Such functions or variables also are not created inside of a component function and hence changing them won't affect your components (components won't be re-evaluated if such variables or functions change and vice-versa)

So long story short: You must add all "things" you use in your effect function **if those "things" could change because your component (or some parent component) re-rendered.** That's why variables or state defined in component functions, props or functions defined in component functions have to be added as dependencies!

Here's a made-up dummy example to further clarify the above-mentioned scenarios:

```jsx
import { useEffect, useState } from 'react';

let myTimer;
  
const MyComponent = (props) => {
  const [timerIsActive, setTimerIsActive] = useState(false);
  const { timerDuration } = props; // using destructuring to pull out specific props values

  useEffect(() => {
    if (!timerIsActive) {
      setTimerIsActive(true);
      myTimer = setTimeout(() => {
        setTimerIsActive(false);
      }, timerDuration);
    }
  }, [timerIsActive, timerDuration]);
};
```

In this example:

* `timerIsActive` is **added as a dependency** because it's component state that may change when the component changes (e.g. because the state was updated)
* `timerDuration` is **added as a dependency** because it's a prop value of that component - so it may change if a parent component changes that value (causing this MyComponent component to re-render as well)
* `setTimerIsActive` is **NOT added as a dependency** because it's that **exception**: State updating functions could be added but don't have to be added since React guarantees that the functions themselves never change
* `myTimer` is **NOT added as a dependency** because it's **not a component-internal variable** (i.e. not some state or a prop value) - it's defined outside of the component and changing it (no matter where) **wouldn't cause the component to be re-evaluated**
* `setTimeout` is **NOT added as a dependency** because it's **a built-in API** (built-into the browser) - it's independent from React and your components, it doesn't change

## `useEffect` for function cleanup

Suppose we have to check if a username has been used at the back-end, in the above scenarios, we saw that the `useEffect` hook ran on every keystroke, and so sending an HTTP request to the back-end server after every keystroke would meaning we would be sending too many requests to the server.

To overcome this issue, we use something called `debouncing` , where we `debounce` the user input, i.e, we wait for a certain amount of time (like 500ms) if the user has stopped typing, and then collect the user input value to send it to the back-end server to check if the username has already been taken.

```jsx
useEffect( () => {
  setTimeout(()=>{
    setFormIsValid(enteredEmail.includes('@') && enteredAge.trim().length > 6)
  }, 500);

return () => {};
}, [enteredEmail, enteredPassword])
```

The highlighted part of the code, where we return an anonymous function, is also called the cleanup function. This clean-up function runs before every new side effect (`useEffect`) function execution, and before the component is removed. It does not run before the first side effect function execution. But thereafter, it will run before every side effect function execution.

Now let's clear the timer after the 500ms have passed using this `clean-up` function in the return statement:

```jsx
useEffect( () => {
  const debounceTimer = setTimeout(()=>{
    console.log('Checking form validity!');
    setFormIsValid(enteredEmail.includes('@') && enteredAge.trim().length > 6)
  }, 500);

  return () => {
    console.log('CLEANUP');
    clearTimeout(debounceTimer);
  };
}, [enteredEmail, enteredPassword])
```

Here, I've added `console.log()` statements to better understand how this works.

When we load the app for the first time, we see "checking form validity!". This is because the side-effect function executes for the first time when the app loads initially. The cleanup function does not run before the first render cycle, hence we do not see "CLEANUP" in the console.

Now, if we start typing in the input textbox, we can see the logs being printed onto the console.

On the first keystroke, we see "CLEANUP" is logged to the console. If we do not type within 500ms, the log "Checking form validity!" is seen in the console. If we type keystrokes within 500ms, we can see multiple "CLEANUP" logs in the console, and then if we stop, after 500ms, we can see "Checking form validity!" is seen on the console.

Hence, the cleanup function executes BEFORE the side effect function can execute.

## Summary of `useEffect`

There are basically 4 ways of using the `useEffect` hook, since it is the second most important React hook.

### 1 .Without the 2nd parameter

```jsx
useEffect(()=>{console.log('useEffect running without 2nd param')})
```

The above code works, but the only problem is that it runs after every component evaluation. So it runs the first time the app is loaded, on every keystroke, on every event that occurs within that component.

### 2. With an empty dependencies array

```jsx
useEffect(() => {console.log('useEffect running with empty dependencies')}, [])
```

The above code runs only once, i.e, when the app loads for the first time. Thereafter it never executes.

### 3. With dependencies in 2nd parameter

```jsx
useEffect(()=>{
    console.log('useEffect running with dependencies')

  }, [enteredName, enteredPassword]
)
```

The above side-effect function runs only when the dependencies mentioned in the array (2nd parameter), change.

### 4. With a clean-up function

```jsx
useEffect(()=>{
  const timerEx = setTimeout(()=>{
    console.log('Checking form validity!')
    setFormIsValid(enteredName.includes('@') && enteredPassword.trim().length > 6)
  }, 500)

  return () => {
    console.log('Clean Up')
    clearTimeout(timerEx)
  }
}, [enteredName, enteredPassword])
```

In the above scenario, the side-effect function runs first when the app loads for the first time, the clean-up function does not run before the first render cycle (i.e, when the app loads for the first time). Thereafter, the clean-up function runs before every time the side-effect function is executed.
