---
description: Handling side-effects and async tasks with Redux.
---

# ♺🥴 Advance Redux

## Side-effects, async tasks & Redux

Reducers must be `pure` , `side-effect free` and `synchronous` functions. They should always take some input, i.e, `old state + actions` and produce the output, which is a new state.

So suppose we have an `action` where we fetch data from an HTTP request. Where should we place our async request? Because in Redux toolkit, the reducer function names are the unique identifiers for the action types that are dispatched.

There are two possible solutions for this problem:

* Inside the **component**, in the `useEffect()` hook. So Redux toolkit does not know about the action until the `useEffect` hook is done executing.
* Write our own `action creators` and do not use the ones that Redux automatically gives us, and add the side-effects inside these action creators.

![Redux with side effects and async tasks.](<../.gitbook/assets/Screenshot 2021-05-20 at 11.39.59.png>)

## Redux & async code

### Front-end code depends on the back-end code

![Frontend and backend interaction.](<../.gitbook/assets/Screenshot 2021-05-20 at 11.49.44.png>)

### Fat Reducers vs Fat Components vs Fat Actions

![Where to put async code and side-effect logic.](<../.gitbook/assets/Screenshot 2021-05-20 at 11.54.54.png>)

## Using `useEffect` with Redux

We can add our side effect code inside of a component, and keep the logic for updating the state in the reducers of the slice in Redux. This will lead to having a `Fat Reducer` and a leaner component.

We get the latest state using the `useSelector` hook provided by `react-redux` library, and then we can update the state on our back-end whenever the state changes, from the reducer. Since the component is subscribed to the state changes from the reducer, whenever the state changes, the component is notified, the component UI changes, and so does the state in the Redux store. The latest state is fetched using `useSelector` and then we use the `useEffect` hook, because we need to send the updated state to the back-end.

```jsx
import {useSelector} from 'react-redux';
import {useEffect} from 'react';

//...
const MyComponent = () => {
  const latestState = useSelector(state => state.cart);

  useEffect(() => {
      fetch('https://redux-cart-225b2-default-rtdb.firebaseio.com/cart.json',
      {
    method: 'PUT', //replaces data in backend
    body: JSON.stringify(latestState)
      })
    }
  , [cart]);
}
```

With this setup, we update the back-end when the Redux store is done updating the state based on the actions dispatched to it's reducers.

**NOTE:** The only problem here is, whenever we load the application for the first time, our initial state is empty, so when we fetch the `latestState`, we get back an empty state, which then replaces the whole data in our back-end with that empty state data.

Let's add a notification bar at the top to show the status of the data being sent to the back-end server:

{% code title="ui-slice.js" %}
```jsx
const initialState = {cartIsShown: false, notification: null}
//...
const uiSlice = createSlice({
  //...
  //...
  reducers: {
    //...
    //...
    showNotification(state, action) {
      state.notification = {
        status: action.payload.status,
        title: action.payload.title,
        message: action.payload.message
      }
    }
  }
})

export const uiActions = uiSlice.actions;
```
{% endcode %}

{% code title="App.js" %}
```jsx
import Notification from './components/UI/Notification';

import {useEffect} from 'react';
import {uiActions} from '../store/ui-slice';
import {useSelector, useDispatch} from 'react-redux';

let isInitial = true; // to not send data when the app loads initially in useEffect.

const App = () => {
  ...
  ...
  const dispatch = useDispatch();
  const cart = useSelector(state => state.cart);
  const notification = useSelector(state => state.ui.notification);

  useEffect(() => {
    const sendHttpReq = async() => {
      dispatch(
    uiActions.showNotification({
      status: 'Sending',
      title: 'Sending...',
      message: 'Sending data to back-end...'
    })
      )
      const response = await fetch('https://firebase-app-link/cart.json',{
    method: 'PUT',
    body: JSON.stringify(cart)
      });
      if(!response.ok) throw new Error('Request failed');
      dispatch(
    uiActions.showNotification({
      status: 'success',
      title: 'Success',
      message: 'Data is sent to back-end server!'
    })
      )
    }

    if(isInitial) {
      isInitial = false;
      return;
    }

    sendHttpReq().catch(error => 
      dispatch(
    uiActions.showNotification({
      status: 'error',
      title: 'Error!!',
      message: error.message
    })
      )
    )
  }, [cart, dispatch]);

}
```
{% endcode %}

## Using an `Action Creator Thunk`

The `action` that we get on our slice, that we export using the `sliceName.actions` , is the default action creator provided to us by `redux toolkit`. When we call them, we create action objects, which we then `dispatch`. These are automatically created `action creators`.

We can also write our own action creators to create our so-called `thunks`. So what is a `thunk`? A `thunk` is a function that delays an `action` until later, until something else is finished.

So we could write an `action creator` as a `thunk`, to write an `action creator`, which does not immediately return the action object, but instead returns another function which eventually returns the action.

![What is a "Thunk"?](<../.gitbook/assets/Screenshot 2021-05-20 at 11.58.32.png>)

We can use these `thunks` to keep our components lean, and not have too much logic in them. From the above example code, we can see how large our `App.js` file has become. Let's create an `action creator thunk` to reduce the logic of fetching and dispatching the `uiActions` from the component, to within our `action creator`.

{% code title="cart-actions.js" %}
```jsx
import {uiActions} from './ui-slice';

export const sendCartData = (cart) => {
  return async(dispatch) => {
    dispatch(
      uiActions.showNotification({
        status: 'sending',
        title: 'Sending...',
        message: 'Sending data to back-end...'
      })
    )

    const sendHttpReq = async() => {
      const response = await fetch('https://firebase-project/cart.json',{
        method: 'PUT',
        body: JSON.stringify(cart)
      });
      if(!response.ok) throw new Error('Request failed!!');
    }

    try{
      await sendHttpReq();
      dispatchFn(
        uiActions.showNotification({
          status: 'success',
          title: 'Success',
          message: 'Sent cart data successfully!',
        })
      );
    } catch(error) {
      dispatchFn(
        uiActions.showNotification({
          status: 'error',
          title: 'Error!!',
          message: error.message,
        })
      );
    }
  }
}
```
{% endcode %}

{% code title="App.js" %}
```jsx
//...
import {useDispatch, useSelector} from 'react-redux';
import {sendCartData} from './store/cart-slice';
//...

let isInitial = true;

const App = () => {
  const dispatch = useDispatch();
  const cart = useSelector(state => state.cart);
  //...
  //...
  useEffect(() => {
    if(isInitial) {
      isInitial = false;
      return;
    }

    dispatch(sendCartData(cart))

  }, [cart, dispatch]);
}
```
{% endcode %}

With this approach, we have a `Fat Action` instead of `Fat Component` or `Fat Reducer`.

### `Action Creator Thunk` for fetching data

{% code title="cart-actions.js" %}
```jsx
//...
//...

export const fetchCartData = () => {
  return async (dispatch) => {
    const sendHttpReq = async () => {
      const response = await fetch(
        'https://redux-cart-225b2-default-rtdb.firebaseio.com/cart.json'
      );
      if (!response.ok) throw new Error('request failed!');
      const data = await response.json();
      return data;
    };

    try {
      const cartData = await sendHttpReq();
      dispatch(cartActions.replaceCart(cartData));
    } catch (error) {
      dispatch(
        uiActions.showNotification({
          status: 'error',
          title: 'Error!',
          message: 'Fetching cart data failed!',
        })
      );
    }
  };
};
```
{% endcode %}

## Debugging Redux apps

We use a browser extension called [`redux devtools`](https://addons.mozilla.org/en-US/firefox/addon/reduxdevtools/) , which help us better understand the actions, payloads, store and other things about the Redux application.

Below is a snapshot of how the tool looks. The left hand side of the tool shows us all the actions that we dispatch, and on the right hand side, we have a bunch of tabs that help us understand what the state is, what it was, and the diff in state for that particular action. We can also perform something called `time-travelling` , where we move our app to a state based on the Redux store data, by pressing `Jump` on the action on the left. This is a very useful and powerful tool for debugging `react-redux` apps!

![](../.gitbook/assets/Screenshot\_2021-04-29\_at\_23.50.05.png)
