---
description: Hook to avoid prop chains.
---

# 🪝 useContext() hook

## Introduction

We use `props` to forward data between components. We usually send the state between different components in this way. However, it will be a problem if we forward our state via multiple components, i.e, we leverage `props` to forward the state data between components, and thus the `props` end up being sent to components that do not require those `prop(s)`.

This problem, where we send unnecessary props to components that do not require the props at all, is called `prop chaining`. To overcome this issue, we use the React Context API.

React Context can be thought of as a component-wide, "behind-the-scenes" state storage API.

## Using `context`

### Syntax

{% code title="context/auth-context.js" %}
```jsx
import React from 'react';

const AuthContext = React.createContext({isLoggedIn: false});

export default AuthContext;
```
{% endcode %}

This creates a context object. The `createContext()` takes a default context, which is basically our App or component wide state. This default context can be a string, a text or even an object. Usually it is an object.

This returns a component, or an object of components. We export this component then, to be used in other components.

When we import the `AuthContext` in a component that we need to use it in, we need to do two things:

* `Provide` the context to that component.
* `Consume` that context in the component we need the props from the parent component.

We need to `Provide` the context to the components that require the state props and to other components nested within them, i.e, their children, using `<AuthContext.Provider></AuthContext.Provider>`

We need to provide the context object that can be consumed, so we need to provide a `value` prop to this `Provider`, which has the state prop which needs to be passed down from `App` to `Navigation`.

{% code title="App.js" %}
```jsx
import React, {useState} from 'react';
import AuthContext from './context/auth-context';
//...
//...
const App = () => {
  const [isLoggedIn, setIsLoggedIn] = useState(false);
  //...
  //...
  return(
    <AuthContext.Provider value={{isLoggedIn: isLoggedIn}}>
      <MainHeader
        isAuthenticated={isLoggedIn}
        onLogout={logoutHandler} 
      />
      <main>
        {!isLoggedIn && <Login onLogin={loginHandler}/>}
        {isLoggedIn && <Home onLogout={logoutHandler}/>}
      </main>
    </AuthContext.Provider>
  );
}
```
{% endcode %}

With this, we no longer require `isAuthenticated={isLoggedIn}` in the `MainHeader` component. Within the `MainHeader` component, we can remove the prop for `isAuthenticated` as well.

Then we need to `Consume` that same context in the specific components where we need those specific state props.

We can consume the context using `useContext()` hook, or `<AuthContext.Consumer></AuthContext.Consumer>`

## Using the `Consumer` tag

The `AuthContext.Consumer` has an anonymous function within it, which receives the `context` to the props in the parameters of this anon function, and returns the JSX code that needs to be rendered in this anon function as well.

### Syntax

```jsx
import AuthContext from '../../context/auth-context';

const Navigation = () => {
  return (
    <AuthContext.Consumer>
      {(ctx) => {
        return (
          //JSX Code that uses ctx.isLoggedIn to render to the DOM.
        );
      }}
    </AuthContext.Consumer>
  );
}
```

This is more of a clustered way of using the consumer. The more elegant way of using a consumer is via the `useContext()` hook.

## Using the `useContext` hook

It's rather simpler to use context using the `useContext`hook.

### Syntax

```jsx
import React, {useContext} from 'react';
import AuthContext from '../../context/auth-context';

const Navigation = () => {
  const ctx = useContext(AuthContext);
  return (
    //JSX Code that uses ctx.isLoggedIn to render to the DOM
  );
}
```

## Custom `Context Provider`

We move all of out state management across components to within the `AuthContext` to make an inbuilt `AuthContext.Provider`

{% code title="context/auth-context.js" %}
```jsx
import React, { useState, useEffect } from 'react';

const AuthContext = React.createContext({
  isLoggedIn: false,
  onLogout: () => {},
  onLogin: (email, password) => {},
});

export const AuthContextProvider = ({ children }) => {
  const [isLoggedIn, setIsLoggedIn] = useState(false);
  useEffect(() => {
    const userLoggedInInfo = localStorage.getItem('isLoggedIn');
    if (userLoggedInInfo === 'LOGGED_IN') {
      setIsLoggedIn(true);
    }
  }, []);
  const loginHandler = (email, password) => {
    localStorage.setItem('isLoggedIn', 'LOGGED_IN');
    setIsLoggedIn(true);
  };
  const logoutHandler = () => {
    localStorage.removeItem('isLoggedIn');
    setIsLoggedIn(false);
  };
  return (
    <AuthContext.Provider
      value={{
        isLoggedIn: isLoggedIn,
        onLogout: logoutHandler,
        onLogin: loginHandler,
      }}
    >
      {children}
    </AuthContext.Provider>
  );
};
export default AuthContext;
```
{% endcode %}

This is to make out `App` component look cleaner and sleek, and move most of the state management related to authentication to within the `AuthContext` itself, which can then be used to wrap out `<App />` component in the `index.js` file.

{% code title="App.js" %}
```jsx
import React, { useContext } from 'react';
import Login from './components/Login/Login';
import Home from './components/Home/Home';
import MainHeader from './components/MainHeader/MainHeader';
import AuthContext from './context/auth-context';

function App() {
  const ctx = useContext(AuthContext);
  return (
    <>
      <MainHeader />
      <main>
        {!ctx.isLoggedIn && <Login />}
        {ctx.isLoggedIn && <Home />}
      </main>
    </>
  );
}

export default App;
```
{% endcode %}

{% code title="index.js" %}
```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import { AuthContextProvider } from './context/auth-context';

ReactDOM.render(
  <AuthContextProvider>
    <App />
  </AuthContextProvider>,
  document.getElementById('root')
);
```
{% endcode %}

## Context Limitations

Use `props` for configuration and `context` for state management across components or even the whole app.

![Context API limitations.](<../../.gitbook/assets/Screenshot 2021-05-20 at 10.04.27.png>)

A better tool for high frequency changes is called `Redux`. You can read more on this tool [here](https://redux.js.org/).

## When to use `props` and avoid `context`

We need to use `props` when we want our component to be reusable, like if we have an `Input` component, which required a bunch of info, and to make this component reusable throughout our App, we need to use `props` instead of `context`.

{% code title="Login.js" %}
```jsx
//...
  //...
    //...
      //...
        <Input
          isValid={emailState.isValid}
          htmlFor="email"
          label="E-Mail"
          type="email"
          id="email"
          value={emailState.value}
          onChange={emailChangeHandler}
          onBlur={validateEmailHandler}
        />
        <Input
          isValid={pwdState.isValid}
          htmlFor="password"
          label="password"
          type="password"
          id="password"
          value={pwdState.value}
          onChange={passwordChangeHandler}
          onBlur={validatePasswordHandler}
        />
```
{% endcode %}

{% code title="UI/Input/Input.js" %}
```jsx
import React from 'react';
import classes from './Input.module.css';

const Input = ({
  isValid,
  htmlFor,
  id,
  label,
  type,
  value,
  onChange,
  onBlur,
}) => {
  return (
    <div
      className={`${classes.control} ${
        isValid === false ? classes.invalid : ''
      }`}
    >
      <label htmlFor={htmlFor}>{label}</label>
      <input
        id={id}
        type={type}
        value={value}
        onChange={onChange}
        onBlur={onBlur}
      />
    </div>
  );
};

export default Input;
```
{% endcode %}
