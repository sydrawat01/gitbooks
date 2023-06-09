# ♺ Intro to Redux

## What is Redux?

Redux is a state management system for `cross-component` or `app-wide state`.

![Cross component / App wide state](<../../.gitbook/assets/Screenshot 2021-05-20 at 10.24.53.png>)

## Redux v/s React Context

![React Context API disadvantages.](<../../.gitbook/assets/Screenshot 2021-05-20 at 10.30.30.png>)

### 1. Complex setup / management

We may have deeply nested JSX code (as seen in the code below) with a lot of context `Providers`, or we may have one huge context provider, but which is not maintainable (as in the code below that). This issue is usually seen in large, enterprise level applications, generally.

```jsx
return (
  <AuthContextProvider>
    <ThemeContextProvider>
      <UIInteractionContextProvider>
        <MultiStepFormContextProvider>
          <UserRegistration />
        </MultiStepFormContextProvider>
      </UIInteractionContextProvider>
    </ThemeContextProvider>
  </AuthContextProvider>
)
```

```jsx
function AllContextProvider() {
  const [isAuth, setIsAuth] = useState(false);
  const [isEvaluatingAuth, setIsEvaluatingAuth] = useState(false);
  const [activeTheme, setActiveTheme] = useState('default');
  //...
  const [ /*x, setX*/] = useState(/*initial state*/);
  
  function loginHandler(email, password) { /*...*/ };
  function signupHandler(email, password) { /*...*/ };
  function changeThemeHandler(newTheme) { /*...*/ };
  //...
  
   return (
     <AllContext.Provider>
       //...
     </AllContext.Provider>
   );
 }
}
```

### 2. Performance

React Context is not good for high frequency updates, but is better for low-latency updates in the state.

[This](https://github.com/facebook/react/issues/14110#issuecomment-448074060) is from Sebastian Markbåge, who is a part of the Facebook React Team:

> My personal summary is that new context is ready to be used for low frequency unlikely updates (like locale/theme). It's also good to use it in the same way as old context was used. I.e. for static values and then propagate updates through subscriptions. It's not ready to be used as a replacement for all Flux-like state propagation.

Redux, on the other hand, does not have these disadvantages.

## How Redux works

### Core Redux Concepts

![How Redux works.](<../../.gitbook/assets/Screenshot 2021-05-20 at 10.51.06.png>)

Redux maintains a `Central Data Store` for the `state` that we want to manage across components or the whole app.

The components that are dependent on the states stored in the central data store, `subscribe` to the states in this central data store, and re-evaluate and re-execute when the state changes in the central data store.

**NOTE:** The **components do no directly mutate/manipulate the state in the central data store**! Instead, we have a `Reducer Function` which mutates the state in the central data store. This reducer function is not the `useReducer()` hook, but more of a general concept of reducer functions \[which transform an input, and spit out an output after performing that transformation, based on the action received, which is a similar concept used in the `useReducer()` hook].

The way we connect the reducer function to the component is via the `Actions`. The component `dispatch` or `trigger` an `Action` . The `Action` is a simple JavaScript object, that describes the kind of action the reducer function should perform. Redux then `forwards` the `Actions` to the `reducer function`, where the `reducer function` reads the description of the desired operation from the `Actions`, and then performs the necessary changes (`transforms the data`) in the state to be updated in the `Central Data (state) Store`.

Once the state in the data store is updated by the reducer function, the `subscribed` components are notified about the change in state so that they can update their UI.

## Simple Example

We run the JavaScript file using `nodeJS`. To get this setup, follow the below steps:

* `NodeJS` needs to be installed beforehand.
* Create a new empty folder, and add a file called `redux-demo.js`
* Open this folder in the terminal and run the following command to install the `redux` external library:

```bash
# if you are using yarn
yarn add redux

# if you are using npm
npm install redux
```

### Installing `redux`

```bash
yarn add redux
```

### Importing `redux` with `nodeJS` syntax

```jsx
const redux = require('redux');
```

### The `Central Store`

The central store is the place where we store our state data, and which will be manipulated via the `reducer function` . We create the store using the `createStore()` function available in Redux.

```jsx
const store = redux.createStore();
```

### The `Reducer` function

The reducer function should be a `pure function`, meaning, the input it gets, should lead to same output, and should not have any side-effects within it. The reducer function has two inputs:

* Old State
* Dispatched Action

The output:

* A new State Object

![New state object.](<../../.gitbook/assets/Screenshot 2021-05-20 at 10.54.44.png>)

```jsx
const conterReducer = (state = {counter: 0}, action) => {
  return {/*...*/}; // can return object, array, variable, etc. with updated value.
}
```

We need to give the default value to state, since when the store is created with the pointer to the reducer function, the reducer function is executed for the first time the code runs, and will initialize the default value on the first run. If we do not do this, it will throw an error saying that the `state is undefined` . This default value is used only for the first time when the reducer function is created. Thereafter, the default value is then ignored and the current state value is used going forward.

After the reducer function is created and connected to the store, to notify that this is the reducer function that will update the state in the store, we need to create a subscription, so as to update the state when the component sends an `action` to the reducer function to update the state.

```jsx
const store = redux.createStore(counterReducer);
```

### The `Subscriber` function

The subscriber usually subscribes to state changes to the store. It sends `actions` to the `reducer fn` to update the state in the store.

The subscription function in this demo code gets the latest snapshot of the state in the central store by calling the `getState()` function on the store that we created earlier (available on the store in Redux).

```jsx
const counterSubscriber = () => {
  const latestState = store.getState();
  console.log(latestState);
}
```

Now we need Redux to be aware of the `subscriber` function so that it knows that the subscriber function should be executed when the state changes in the store. This is done using the `subscribe()` function that is available in Redux.

```jsx
store.subscribe(counterSubscriber);
```

### Creating and dispatching an `action`

Action is a JavaScript object with property `type`, which is usually a string, but can be any data type.

Besides calling `getState` and `subscribe` methods on the Redux `store`, we can also call `dispatch()` which dispatches the action from the subscriber, to the reducer.

```jsx
store.dispatch({type: 'increment'})
```

#### The final code:

```jsx
const redux = require('redux'); // 1. import redux in nodeJS syntax

const counterReducer = (state = 0, action) => { // 3. create the reducer fn
  return {
    counter: state.counter + 1
  }
}

const store = redux.createStore(counterReducer) // 2. createStore(), 4. add reducer fn

const counterSubscriber = () => { // 5. create the  subscribe fn
  const latestState = store.getState(); // 6. getState()
  console.log(latestState);
}

store.subsribe(counterSubscriber); // 7. subscribe to store for state changes.

store.dispatch({type: 'increment'}) // 8. dispatch an action to the reducer.
```

#### To run the above file in `nodeJS`:

```jsx
node redux-demo.js
```

#### The output:

```jsx
{counter: 2}
```

The counter state is `2` because, initially when the reducer is created and executed, i.e, when the store is created, the default `counter` value is set to `0` (from the parameters), and when the `counterReducer` is executed for the first time, the counter value increments to `1`. Then, when we dispatch the action `increment` to the reducer, the counter gets incremented by 1 again. This causes the state to change from `1` to `2`. Due to this change, the subscriber `counterSubscriber` gets executed, which logs the `latestState` which is actually `store.getState()`, and hence we see the result `{counter: 2}`.

To avoid counter increment at initialization, we have the `action` `type` being sent from the `dispatch()` function call, which we can check in the reducer function, and based on the action type, we can change the state.

```jsx
const counterReducer = (state = {counter: 0}, action) => {
  if(action.type === 'increment') {
    return {
      counter: state.counter + 1
    }
  }
  return state;
}
```

With the above code changes, we can see when we run the `redux-demo.js` file again, the output is:

```jsx
{ counter: 1 }
```

The counter is initially set to 0 by default and is only updated IF the action is `increment`, which is dispatched to the reducer.

The same can be followed to decrement the counter value at `decrement` action, which again needs to be dispatched to the reducer via the `dispatch()` call on the `store` object.

```jsx
const counterReducer = ({/*...*/}) => {
  //...
  //...
  if(action.type === 'decrement') {
    return {
      counter: state.counter - 1
    }
  }
}
```

Now if we have multiple `increment` and `decrement` actions dispatched to the reducer, we will have an output like so:

```jsx
store.dispatch({ type: 'increment' });
store.dispatch({ type: 'increment' });
store.dispatch({ type: 'increment' });
store.dispatch({ type: 'increment' });
store.dispatch({ type: 'decrement' });


// OUTPUT

➜ node redux-demo.js
{ counter: 1 }
{ counter: 2 }
{ counter: 3 }
{ counter: 4 }
{ counter: 3 }
```

Hence, using `redux` library is not limited to React, but it can be used with any project in JavaScript, and also has been implemented in various other programming languages.

#### Final code for reference:

```jsx
//import redux library using nodeJS syntax
const redux = require('redux');

//declare the reducer function with state and action params
const counterReducer = (state= {counter: 0}, action) => {
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

//create the store and attach reducer function
const store = redux.createStore(counterReducer);

//create subscriber function
const counterSubscriber = () => {
  const latestState = store.getState();
  console.log(latestState);
}

//connect subscriber function to SUBSCRIBE to the store
store.subscribe(counterSubscriber);

//setup actions to send to reducer to trigger state changes
store.dispatch({ type: 'increment' });
store.dispatch({ type: 'increment' });
store.dispatch({ type: 'increment' });
store.dispatch({ type: 'increment' });
store.dispatch({ type: 'decrement' });

// OUTPUT

➜ node redux-demo.js
{ counter: 1 }
{ counter: 2 }
{ counter: 3 }
{ counter: 4 }
{ counter: 3 }
```
