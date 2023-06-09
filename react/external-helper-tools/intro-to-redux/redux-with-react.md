---
description: Using the base Redux package with React
---

# ♺⚛ Redux with React

## Installing the dependencies

We need to add the `redux` standalone library, as well as a React specific Redux library called `react-redux` , which makes creating the Redux store, connecting the subscriber to the store, and other such common tasks around Redux that are made easier with this library.

```bash
yarn add redux react-redux
```

## Creating the `store`

In the react project, inside the `src/` folder, create another folder called `store/` . This is just a naming convention, which is followed for Redux files, but you can name it anything. In this folder, we create an `index.js` file, which holds the logic to creating the `store` in Redux.

After creating the store and connecting it with the reducer function, we need to export it, so that it can be subscribed to by the components or the whole app with a subscriber function. For the store to be available to other components, we need to `provide` this store to our react app.

{% code title="store/index.js" %}
```jsx
import { createStore } from 'redux';

const counterReducer = (state = {counter: 0}, action) => {
  if(action.type === 'increment') {
    return {
      counter: state.counter + 1
    }
  }
  if(action.type === 'decrement') {
    return {
      counter: state.counter - 1
    }
  }
return state;
}

const store = createStore(counterReducer);

export default store;
```
{% endcode %}

## `Providing` the `store`

We will provide the store at the highest level of the component tree, i.e, at the place the

`<App />` component is being rendered, in the `src/index.js` file.

It is not necessary that we wrap the root element in our app with the provider, we can also wrap nested components with the provider.

We then wrap our `<App />` component with the `Provider` :

{% code title="src/index.js" %}
```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';

import './index.css';
import App from './App';

ReactDOM.render(
  <Provider>
    <App />
  </Provider>,
  document.getElementbyId('root')
);
```
{% endcode %}

The wrapped component(s) and their immediate and nested child component(s) will thereafter have access to `Redux` now.

But we still need to specify which store we need to access, so this is done by importing the `store` that we had exported in the previous code, in the `store/index.js` file. This imported store needs to be set as a value to the prop in the `<Provider>` where we wrap our component(s) that need access to the store.

{% code title="src/index.js" %}
```jsx
//...
import store from './store/index';
//...

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root');
```
{% endcode %}

## Access `store` data in React components

We import the `useSelector` hook from the `react-redux` library.

```jsx
import {useSelector} from 'react-redux';
```

We can also import `useStore` hook instead of the `useSelector` hook, which would give us a more direct access to the store, but the `useSelector` hook is more convenient to use because it helps us to select a part of the state managed by the store, and this is very useful in a very large state that is being access from the store.

The `useSelector` hook accepts a function as a parameter, where we get return the state property or the part if the state the we need to use. Here we will use an anonymous function.

```jsx
const counter = useSelector(state => state.counter);
```

The good thing about using the `useSelector` hook is, when we use it, `react-redux` will automatically set up a subscription connection to the Redux store for this component. So our component will be updated and receive the updated store counter value automatically, whenever the data changes in the store.

If the component is unmounted from the DOM, `react-redux` will automatically clear the subscription for the component.

## Dispatch `actions` from the component

For dispatching actions to the `react-redux` store, we use the `useDispatch()` hook. This does not accept any parameters, but instead it returns a `dispatch` function, which we can execute against our Redux `store`.

We can use this dispatch function returned from this hook to be executed on events, i.e, on event handlers.

```jsx
import {useDispatch} from 'react-redux';
//...
//...

  const dispatch = useDispatch();
  const incrementHandler = () => {
    dispatch({type: 'increment'})
  }
  const decrementHandler = () => {
    dispatch({type: 'decrement'});
  }
//...
//...
   <button onClick={incrementHandler}>Increment</button>
   <button onClick={decrementHandler}>Decrement</button>
```

## Attaching `payloads` to `actions`

Suppose we want to increment the counter value by 5. For this, we set up a new `button` in our component, and add a click listener to it which dispatches an action for type `incrementBy5` . This action has to now be hard-coded in the `counterReducer` function in the place where we create the Redux store, to increment the counter value by 5 each time the button is clicked.

This scenario of hard-coding the `type` of action being sent from the component is fine for smaller applications, but for larger applications, this is not an efficient way. Hence we attach `payloads` or extra data along with the `type` in the dispatch function.

{% tabs %}
{% tab title="store/index" %}
{% code title="store/index.js" %}
```jsx
//...
//...
const counterReducer = (...) => {
  //...
  //...
  if(action.type === 'increase') {
    return {
      counter: state.counter + action.amount
    }
  }
}
```
{% endcode %}
{% endtab %}

{% tab title="Counter" %}
{% code title="src/components/counter.js" %}
```jsx
//...
//...

const increaseCounter = () => {
  dispatch({type: 'increase', amount: 5})
}
//...
  //...
    //...
      <button onClick={increaseCounter}>Increment by 5</button>
```
{% endcode %}
{% endtab %}
{% endtabs %}
