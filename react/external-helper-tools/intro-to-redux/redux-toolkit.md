---
description: >-
  Third-party package developed by Redux team for better Redux store state
  management in React apps.
---

# ♺⚛ Redux Toolkit

## Challenges with complex state management

* `action` types can get exhausting. For different state properties, we will have different dispatch `type` and their corresponding `actions`, along with their `payloads`. This can get cumbersome to manage in a very large project.
* More data in state means more data to manage, and more data that we would need to return from within the check of `action.type` . We would need to copy a lot of redundant state properties just to update one state property.
* This can lead to a very large `reducer` function in the Redux `store` which becomes difficult to manage in the end. This is the same problem that we faced in React `context` API, where we declare a very large singular context with multiple states,i.e, complex setup.

All these above problems are solved by using another library called `redux-toolkit`, which is developed by the same team/person who developed `redux`. It is just an extra package that makes working with Redux a lot easier and convenient.

## `Redux ToolKit`

### Installing the `redux` `toolkit`

```bash
yarn add @reduxjs/toolkit
```

With this installed, we can remove the `redux` dependency from our project, since `toolkit` comes bundled with `redux`.

### Creating a `slice`

```jsx
import { createSlice } from '@reduxjs/toolkit';
```

We can also import the `createReducer()` hook from the `redux-toolkit` to create a reducer with certain enhancements, but the `createSlice()` hook is more powerful than the `createReducer`. With `createSlice()` we create a slice of our global state. We could create different slices in different files, to make our code more maintainable.

The `createSlice` takes in an object which has:

* A `name` property.
* An `initialState`  which is set to the default or initial state.
* an object (or a map ) called `reducers` which contains the list of all the `reducer functions` that this `state slice`  needs.

```jsx
createSlice({
  name: 'Counter',
  initialState, //ES6 syntax shorthand for initialState: initialState
  reducers: {
    increment() {},
    decrement() {},
    increase() {},
    toggleCounter() {},
  }
})
```

Every method in the `reducers` object is kind of an action type, which receives `state` i.e, the current state for that slice, and no `action` parameter, because the reducer function itself is called based upon the action/trigger by Redux.

So now, we do not need the `if` checks, but instead we will identify these reducer functions and dispatch actions that target them.

Within these functions, we are now allowed to mutate the state directly, which we were previously not allowed to do! It seems like we are mutating the state directly, but behind the scenes, the Redux `toolkit` uses another library called `immer`, which takes care of this.

It detects code like this, where we directly mutate the state, and it automatically clones the existing state, create a new state, keep all the state that we are not editing, and override the state which we are editing in an immutable way.

It is easier for us to work with Redux because we do not have to make a copy of the state manually and keep all the code that we are not changing. So we just change the state that we want to update, and internally it is translated into immutable code.

```jsx
createSlice({
  //...
  //...
  reducers: {
    increment(state) {
      state.counter++;
    },
    decrement(state) {
      state.counter--;
    },
    increase(state,action) {
      state.counter += action.payload;
    },
    toggleCounter(state) {
      state.showCounter = !state.showCounter;
    }
  }
})
```

### Connect `sliced state`

```jsx
const counterSlice = createSlice({
  name: //...
  initialState: //...
  reducers: {/*...*/}
})
```

We have to register this `counterSlice` , which is our state slice, with the Redux `store` .

We can do it as follows \[**BAD PRACTICE**]:

```jsx
const store = createStore(counterSlice.reducers);
```

This works in theory, but will not be good for larger projects, where we have more than one slice of our state, and each slice will have their own set of reducer functions, so ideally this is not the best solution.

\[**BETTER SOLUTION]**

This problem can be overcome by ditching the `createStore` hook from the `redux` library, and instead using a new hook from the `reduxjs/toolkit` library, called `configureStore`.

```jsx
import {createStore} from 'redux';
import { createSlice, configureStore } from '@reduxjs/toolkit';
```

This hook also creates a store, but it makes merging multiple state reducers into one reducer easier thereafter. In the `configureStore` hook, we now pass an object instead of a reducer function pointer. This object is a configuration object that is needed to configure the store with the appropriate config.

The `configureStore` hook takes in a config object which contains:

* `reducer`, which can have a single reducer, or an object/map of reducers with any key with respective value pairs.
  *   **Single reducer:** where we combine all the `reducers` object from the `counterSlice` of the state and merge it into one big reducer.

      ```jsx
      const store = configureStore({
      reducer: counterSlice.reducer //combines all reducers from counterSlice
      })
      ```
  *   **A map of reducers:** where we have an object with key-value pairs, which in the end is combined into one big reducer with all the different reducers from different slices of the state.

      ```jsx
      const store = configureStore({
      reducer: { // combines all reducers from `counter` and `auth` maps
        counter: counterSlice.reducer, //combines all reducers from counterSlice
        auth: authSlice.reducer, //combines all reducers from authSlice
      }
      })
      ```

### Dispatching the `actions`

The `createSlice` hook automatically creates unique identifiers for our different reducers. To get a hold of these action identifiers, we can use the following `dot` syntax:

```jsx
counterSlice.actions.<function_name_for_action>
```

These are called `action creators`, and will create an `action object` for us in the end.

```jsx
counterSlice.actions.increment()
```

This will create an action object with the unique identifier, like `{ type: 'some-unique-identifier-for-increment' }` . With this, we do not need to worry about creating action identifiers and creating the action objects on our own. Redux will do it for us behind the scenes.

We usually export all of our `action creators` to be used in a component, from where we will dispatch these actions:

```jsx
export const counterActions = counterSlice.actions;

//component file --> Counter.js
import {counterActions} from '../store/index.js';

//...
//...
const incrementHandler = () => {
  dispatch(counterActions.increment())
}

const increaseBy5 = () => {
  dispatch(counterActions.increase(5)) // {type: 'UNIQUE_IDENTIFIER_BY_TOOLKIT', playload: 5}
}
```

In case we have payloads in our reducer functions, we can either pass an object with key-value pairs, or we can send the data directly, but the key-value pair will have the default key as `payload`, so we need that same key to access the data in the reducer function which has this payload.

## Working with multiple `slices`

Suppose we want to manage the counter state data and an authentication state data in the same Redux store. We could create two separate slices, since the two state are not related to each other.

### Create `initialState`

So we create different `initialStates` for `counter` and `auth`.

```jsx
const initialCounterState = {counter: 0, showCounter: false};
const initialAuthenticationState = {isAuthenticated: false};
```

### Create the `slice`

Then, we create a new slice for the authentication state, with the same object properties, but with different values.

```jsx
const authSlice = createSlice({
  name: 'auth',
  initialState: initialAuthenticationState,
  reducers: {
    login(state) {
      state.isAuthenticated: true;
    }
    logout(state) {
      state.isAuthenticated: false;
    }
  }
})
```

### Add `slice` to `store`

Next, we need to add this slice to our Redux store.

Previously, we had the following code, that only connected the `counterSlice` to the Redux store:

```jsx
export const store = configureStore({
  reducer: counterSlice.reducer; // combines all reducers into 1 big reducer fn.
})
```

So now, we will have to add an object to the `reducer` property inside the `configureStore` hook provided by `reduxjs/toolkit` ./code

```jsx
export const store = configureStore({
  reducer: { // combines all reducers from all the slices into 1 big reducer fn.
    counter: counterSlice.reducer; //combines all reducers from counterSlice into 1
    auth: authSlice.reducer; //combines all reducers from authSlice into 1
  }
})
```

### Create `actions` to be dispatched via the component

```jsx
export const authActions = authSlice.actions;
```

### Reading the state value using `useSelector()`

Since we have more than one state object, we need to change the way we read the values of these states using the `useSelector` hook from `react-redux`. We need to use the `key` name that we had provided to the reducers in the `reducer` property in the `configureStore` hook where we created the store and linked our `reducer functions`.

```jsx
const isAuthenticated = useSelector((state) => state.auth.isAuthenticated);
const counter = useSelector((state) => state.counter.counter);
const show = useSelector((state) => state.counter.showCounter);
```

## Code Splitting

Since the store file contains the state slices, this file can become really large especially in a large React app, which has many state slices, and therefore becomes difficult to maintain and manage. It is common practice to move out our state slices into their own individual files, so that the `store/index.js` file is easy to manage, and is readable.

* Move the `initialState` for the slice into the state slice file.
* Move the `createSlice()` return value into the state slice file.
* Move the slice `actions` into the state slice file.
* Return only the state slice `reducer` from this state slice file and import only their respective reducers to be used in the `index,js` file for the `configureStore` hook.

By making the above changes, we would need to change the imports as well, where ever necessary.

{% code title="store/auth.js" %}
```jsx
import { createSlice } from '@reduxjs/toolkit';

const initialAuthState = { isAuthenticated: false };

const authSlice = createSlice({
  name: 'auth',
  initialState: initialAuthState,
  reducers: {
    login(state) {
      state.isAuthenticated = true;
    },
    logout(state) {
      state.isAuthenticated = false;
    },
  },
});

export default authSlice.reducer;

export const authActions = authSlice.actions;
```
{% endcode %}

{% code title="store/counter.js" %}
```jsx
import { createSlice } from '@reduxjs/toolkit';

const initialCounterState = { counter: 0, showCounter: true };

const counterSlice = createSlice({
  name: 'counter',
  initialState: initialCounterState,
  reducers: {
    increment(state) {
      state.counter++;
    },
    decrement(state) {
      state.counter--;
    },
    increase(state, action) {
      state.counter += action.payload;
    },
    toggleCounter(state) {
      state.showCounter = !state.showCounter;
    },
  },
});

export default counterSlice.reducer;

export const counterActions = counterSlice.actions;
```
{% endcode %}

Finally, our `index.js` file is easy to maintain and readable:

{% code title="store/index.js" %}
```jsx
import { configureStore } from '@reduxjs/toolkit';

import authReducer from './auth';
import counterReducer from './counter';

const store = configureStore({
  reducer: { counter: counterReducer, auth: authReducer },
});

export default store;
```
{% endcode %}
