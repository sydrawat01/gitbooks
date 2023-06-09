---
description: Hook for complex states, an alternative to useState() hook.
---

# 🪝 useReducer() hook

## State management

![Introducing useReducer() for state management.](<../.gitbook/assets/Screenshot 2021-05-19 at 16.31.20.png>)

Let's consider the following scenario:

{% code title="Login.js" %}
```jsx
import React, { useState, useEffect } from 'react';
import Card from '../UI/Card/Card';
import classes from './Login.module.css';
import Button from '../UI/Button/Button';

const Login = ({ onLogin }) => {
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
      enteredEmail.includes('@') && event.target.value.trim().length > 6
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
    onLogin(enteredEmail, enteredPassword);
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

In the above code, we can see the highlighted functions of the `useState` hooks and their states. Ideally, when updating the state, we use the anonymous function within the state update function to update the state according to the previous state. But in the above cases, we can clearly see that the state update depends on a different state other than it's previous state! In such a case we cannot use the anonymous function with reference to the previous state to update the state.

From the code above, we can clearly see:

* in `emailChangeHandler` , the `setFormIsValid()`is using the state `enteredEmail` to update the `formIsValid` state.
*
  * in password
*
  * in `validateEmailHandler` , the `setEmailIsValid()` is using the state `enteredEmail` to update the `emailIsValid` state.
*
  * in `validatePasswordHandler`, the `setPasswordIsValid()` is using the state `enteredPassword` to update the `passwordIsValid` state.

This is the place where **Reducers** come in handy, where we are updating a state that is dependent on another state.

## Understanding `useReducer()`

### Syntax

```jsx
const [state, dispatchFn] = useReducer(reducerFn, initialState, initFn);
```

![useReducer() hook syntax breakdown.](<../.gitbook/assets/Screenshot 2021-05-19 at 16.42.31.png>)

The `useReduer()` returns two things, similar to the way `useState()` hook user to return two things, which are then stored in an array by using array de-structuring.

The first parameter is the `state`, similar to the way in `useState()`

The second parameter is the `dispatchFn` that dispatches an action to the `useReducer()` hook.

In the `useReducer()` hook, the first parameter is `reducerFn` , that gets the `prevState` and the `action` from the `dispatchFn`. This function is triggered automatically, which receives the latest state snapshot and returns the new, updated state. The second and third parameters are `initialState` and `initFn` , where we can set the initial state, and where we can set the initial state if our state is big and complex to be initialized in a parameter, respectively.

### Using `userReducer()`

In the code above, we can see we have a lot of different states, that depend on other states.

```jsx
const [enteredEmail, setEnteredEmail] = useState('');
const [emailIsValid, setEmailIsValid] = useState();
const [enteredPassword, setEnteredPassword] = useState('');
const [passwordIsValid, setPasswordIsValid] = useState();
const [formIsValid, setFormIsValid] = useState(false);
```

We can use one/two reducers here to minimize the states that we manage via `useState` , and instead, use reducers that help simplify updating the state where the state update is dependent upon another state rather than it's own previous state.

The previous code is commented out. We have also added the `debounce` functionality with the `useEffect` hook (with a clean-up functionality), which helps us with the form validity as a whole.

{% code title="Login.js" %}
```jsx
import React, { useState, useEffect, useReducer } from 'react';
import Card from '../UI/Card/Card';
import classes from './Login.module.css';
import Button from '../UI/Button/Button';

const emailReducer = (state, action) => {
  if (action.type === 'USER_EMAIL') {
    return {value: action.value, isValid: action.value.includes('@')}
  }
  if (action.type === 'EMAIL_BLUR') {
    return {value: state.value, isValid: state.value.includes('@')}
  }
  return {value: '', isValid: false}
}

const pwdReducer = (state, action) => {
  if (action.type === 'USER_PWD') {
    return {value: action.value, isValid: action.value.trim().length > 6}
  }
  if (action.type === 'PWD_BLUR') {
    return {value: state.value, isValid: state.value.trim().length > 6}
  }
  return {value: '', isValid: false}
}

const Login = ({ onLogin }) => {
  /*const [enteredEmail, setEnteredEmail] = useState('');
  const [emailIsValid, setEmailIsValid] = useState();
  const [enteredPassword, setEnteredPassword] = useState('');
  const [passwordIsValid, setPasswordIsValid] = useState(); */
  const [formIsValid, setFormIsValid] = useState(false);

  const [emailState, dispatchEmail] = useReducer(emailReducer, {value: '', isValid: null})
  const [pwdState, dispatchPwd] = useReducer(pwdReducer, {value: '', inValid: null})

  const {isValid: emailIsValid} = emailState;
  const {isValid: pwdIsValid} = pwdState;

  useEffect( () => {
    const debounceTimer = setTimeout( () => {
      console.log('Checking form validity!');
      setFormIsValid(emailIsValid, pwdIsValid)
    }, 500);

    return () => {
      console.log('Clean Up!');
      clearTimeout(debounceTimer)
    };
  }, [emailIsValid, pwdIsValid]);

  const emailChangeHandler = (event) => {
    /*setEnteredEmail(event.target.value);
    setFormIsValid(
      event.target.value.includes('@') && enteredPassword.trim().length > 6
    );*/
    dispatchEmail({type: 'USER_EMAIL', value: event.target.value})
  };

  const passwordChangeHandler = (event) => {
    /*setEnteredPassword(event.target.value);
    setFormIsValid(
      enteredEmail.includes('@') && event.target.value.trim().length > 6
    );*/
    dispatchPwd({type: 'USER_PWD', value: event.target.value})
  };

  const validateEmailHandler = () => {
    //setEmailIsValid(enteredEmail.includes('@'));
    dispatchEmail({type: 'EMAIL_BLUR'})
  };

  const validatePasswordHandler = () => {
    //setPasswordIsValid(enteredPassword.trim().length > 6);
    dispatchPwd({type: 'PWD_BLUR'})
  };

  const submitHandler = (event) => {
    event.preventDefault();
    //onLogin(enteredEmail, enteredPassword);
    onLogin(emailState.value, pwdState.value)
  };

  return (
    <Card className={classes.login}>
      <form onSubmit={submitHandler}>
        <div
          className={`${classes.control} ${
            emailState.isValid === false ? classes.invalid : ''
          }`}
        >
          <label htmlFor="email">E-Mail</label>
          <input
            type="email"
            id="email"
            value={emailState.value}
            onChange={emailChangeHandler}
            onBlur={validateEmailHandler}
          />
        </div>
        <div
          className={`${classes.control} ${
            pwdState.isValid === false ? classes.invalid : ''
          }`}
        >
          <label htmlFor="password">Password</label>
          <input
            type="password"
            id="password"
            value={pwdState.value}
            onChange={passwordChangeHandler}
            onBlur={validatePasswordHandler}
          />
        </div>
        <div className={classes.actions}>
          <Button 
        type="submit"
        className={classes.btn}
        disabled={!formIsValid}
      >
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

In the above code, we have used `useEffect` hook as well, for managing the state of the form, if it is valid or not, depending on the keystroke the user enters. We also use `debouncing` , where the validity check waits for the user for up to 500ms, and then performs the validity check.

In the dependencies array of the second parameter of the `useEffect` hook, we specify the object properties `emailState.isValid` and `pwdState.isValid` because we now want to perform validity check only if the email or password `isValid` property is `false` .

We do this to avoid calling the `useEffect` hook whenever there is a change in the `emailState` or `pwdState` , and to be precisely run only when their `isValid` property changes.
