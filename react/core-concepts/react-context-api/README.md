---
description: >-
  Create context to make data globally available across components or throughout
  the app.
---

# 🪝 React Context API

## Prop chaining problem

Suppose we have 4 components: `A`, `B`, `C` and `D` ; all nested within each other. We want to pass the state or props down to `D` from `A`. So here, we would need to pass the same state or props to `B` and `C` as well, even though these components might not need these state or props.

Prop chaining leads to components being **less reusable** , and since we always need to pass `props` that are not related to the component, and increase code complexity.

To overcome this problem, we use `React Context API`.

Consider the following example:

Suppose we want to add authentication to our app, and we add the following logic:

{% code title="Cockpit.js" %}
```jsx
const Cockpit = props => {
  //...
  return(
    //...
    <button onClick={props.login}>Login</button>
  );
}
```
{% endcode %}

{% code title="App.js" %}
```jsx
class App extends Component {
  //...
  state = {
    //...
    authenticated: false,
    //...
  }
  //...
  loginHandler = () => {
    this.setState({authenticated: true});
  }
  //...
  render() {
    //...
    return(
      <Cockpit login={this.loginHandler}
      <Persons isAuthenticated={this.props.authenticated}
    );
  }
}
```
{% endcode %}

Now, we want to add the `login` logic to each `Person`, but we have only `Persons` component, so we need to propagate the `authenticated` prop to `Persons` component, which will then send it as `props` to `Person` component:

{% code title="Persons.js" %}
```jsx
class Persons extends Component {
  //...
  render() {
    //...
    return(
      //...
      <Person isAuth={this.props.isAuthenticated}
    );
  }
}
```
{% endcode %}

Finally, we get the `authenticated` prop in `Person` component.

{% code title="Person.js" %}
```jsx
class Person extends Component {
  //...
  render() {
    const {click, name, change, isAuth} = this.props;
    return(
      <div>
        <h1 onClick={click}>Hello React, I'm {name}</h1>
        {isAuth ? <p>Authenticated!</p> : <p>Please login!</p>}
      </div>
    );
  }
}
```
{% endcode %}

This works absolutely fine, but the problem here is that our `Persons` component had nothing to do with the `authentication` prop, and it was passed through it to `Person` component, thereby complicating the code for itself and having unnecessary props in it, even though it is not required in `Persons` component. This is the problem of `prop chains`.

This makes components less reusable, since we will have to always pass `props` that are not related to the component, and hence increase complexity of code.

To overcome this issue, we have `React Context`, which helps us overcome the issues of `prop chaining`. It helps us transfer multiple state/props across multiple layer of components, say from `A` to `D`, without bothering `B` and `C`, even though components `B, C and D` are nested within each other.

## React Context API

`React.createContext()` allows us to initialize our context with a default value. The context is a globally available JS object. Though globally available is not correct, we decide where the context will be available.

The `createContext` method takes a JS object (or a string, array, etc) that can be passed between React components without using props behind the scenes.

{% code title="context/AuthContext.js" %}
```jsx
import React, {Component, createContext} from 'react';

const AuthContext = createContext({
  authenticated: false,
  login: () => {}
});

export default AuthContext;
```
{% endcode %}

We will use this context in App.js, and wrap the components that require the authentication context.

{% code title="App.js" %}
```jsx
import AuthContext from '../context/AuthContext';

class App extends Component {
  //...
  render() {
    //...
    return(
      //...
      <AuthContext.Provider
        value={{
          authenticated: this.state.authenticated,
          login: this.loginHandler
        }}
      >
        <Cockpit />
        {persons}
      </AuthContext.Provider>
    );
  }
}
```
{% endcode %}

The `AuthContext.Provider` accepts a `value` `prop`, so it does not matter what default values we pass into the `createContext()` method. The default values will be used when there is no `value` prop being used in the `Provider`.

Now, we want to use this context in our `Persons` component:

{% code title="Persons.js" %}
```jsx
import AuthContext from '../../context/AuthContext';

class Persons extends Component {
  //...
  render() {
    return(
      <AuthContext.Consumer>
        {() => {this.props.persons.map((person, index)=>{
          return(
            <Person
              name={person.name}
              age={person.age}
            />
          );
        })}}
      </AuthContext.Consumer>
    );
  }
}
```
{% endcode %}

We use the `Consumer` to use our context in the `Persons` component. But, the `Consumer` does not take JSX within it's code block, so we need to return an anonymous function here to return the JSX code.

This function will be executed for us, by the `AuthContext.Consumer`(or React Context API), will take `context` as an object. That is how we get access to the context object when we consume it.

But this is not the solution we needed. We had to skip passing the `authenticated` prop to `Persons`, and directly should be sent to the `Person` component.

So, in the `Person` component, we will import `AuthContext` and use it to wrap whatever code we need to be accessed by the context. In our case here, we need it only at one place.

{% code title="Person.js" %}
```jsx
import AuthContext from '../../../context/AuthContext';

class Person extends Component {
  //...
  const {name, ..., ...} = this.props;
  render() {
    return(
      <Aux>
        <AuthContext.Consumer>
          {(context) => (
            context.authenticated ? <p>Authenticated!</p> : <p>Please Login!</p>
          )}
        </AuthContext.Consumer>
        <h1> Hi, I am {name}</h1>
      </Aux>
    );
  }
}
```
{% endcode %}

Before moving forward, we need to `consume` the `context` in the `Cockpit` component as well, since the `login button` is the one that initiates the login authentication. Then we access the `login` prop using the `context` that is defined in the `Provider` of `App` component.

{% code title="Cockpit.js" %}
```jsx
//Cockpit.js
import AuthContext from '../../context/AuthContext';

const Cockpit = props => {
  //...
  return(
    <div>
      //...
      <AuthContext.Consumer>
        {(context) => (
          <button onClick={context.login}>Login</button>
        )}
      </AuthContext.Consumer>
    </div>
  );
}
```
{% endcode %}

Now, we can remove all the JSX element properties that were being passed to `Persons` component and their respective `props`. Now the context will be directly used wherever we provide the `Consumer` to access the `context` in it's anonymous function call.

This is really useful when we have long chains of data, and don't want to pass the data from component-to-component-to-component, which can lead to redundancy, complex and unreadable code, and most importantly, they make components less reusable.

### Class-based components

A more elegant way of using context in class-based components using `contextType()`, and in functional components using `useContext()`.

#### `contextType()`

In class-based components, there is an alternate way to use the `context` instead of using an anonymous function. In using the older way, we only have access to the `context` within the component we wrap our `consumer` around and render `<AuthContext.Consumer>`.

What if we need the `context` in `componentDidMount()` hook, where we need the authentication status as well? The new way to access `context` was added in React v16.6^

We can add a special `static` context property called `contextType`. It has to be static so that we can instantiate the `contextType` without the need to create an object of the class component.

{% code title="Person.js" %}
```jsx
import AuthContext from '../../../context/AuthContext';

class Person extends Component {
  //...
  static contextType = AuthContext;
  componentDidMount() {
    console.log('[Person.js] componentDidMount');
    console.log(this.context.authenticated);
  }
  render() {
    return(
      <Aux>
        {this.context.authenticated ? (<p>Authenticated</p> : <p>Please login</p>)}
      </Aux>
    );
  }
}
```
{% endcode %}

This allows React to automatically connect this class-based component to the context behind the scenes, and it gives a new property on this component. the `this.context` property. We can then use this property in `componentDidMount` and `render` methods. We need to remove the `<AuthContext.Comsumer>` tags to use it in the `render` method, since we have access to the context using `this.context`, and skip using the anonymous function call to access `context`.

### Functional Components

#### `useContext()`

In functional components, the above method is not available because we cannot setup a static `contextType` property. We can use the `useContext()` hook for this purpose.

{% code title="Cockpit.js" %}
```jsx
import React, {useState, useEffect, useRef, useContext} from 'react';
import AuthContext from '../../context/AuthContext';

const Cockpit = props => {
  const authContext = useContext(AuthContext);
  //...
  return(
    <div>
      //...
      <button onClick={authContext.login}>Log In</button>
    </div>
  );
}
```
{% endcode %}

The `useContext` hooks allows us to access the context anywhere within the component, but only within the component body. To use it in `return`, we need to get rid of the `<AuthContext.Consumer>` tags.
