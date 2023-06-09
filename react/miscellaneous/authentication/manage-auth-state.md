---
description: Using redux to manage auth state across the React app.
---

# 🔐 Manage Auth State

We can use `Redux` or `React Context` to set up a global / app-wide state. Since the authentication state is not in any way going to affect app performance, we can use React Context here. But I prefer using Redux here.

## Redux for Auth State

We will use the `@reduxjs/toolkit` to manage our app wide state. So first we need to install all the dependencies.

### Installing dependencies

```bash
yarn add @reduxjs/toolkit react-redux
```

### Creating the `authSlice`

Now, we create the `authSlice` to create our reducers, provider default actions and manage state in our Redux store.

{% code title="auth-slice.js" %}
```jsx
import {createSlice} from '@reduxjs/toolkit';

const initialState = {
  token: '',
  isLoggedIn: false,
}

const authSlice = createSlice({
  name: 'auth',
  initialState,
  reducers: {
    login(state, action) {
      state.token = action.payload,
      state.isLoggedIn = !!action.payload,
    }
    logout(state, action) {
      state.token = null,
      state.isLoggedIn = false,
    }
  }
})

export default authSlice.reducer;
export const AuthActions = authSlice.actions;
```
{% endcode %}

### Connect Reducers to Store

Now, we need to attach this reducer to the `central Redux store`.

{% code title="src/store/index.js" %}
```jsx
import {configureStore} from '@reduxjs/toolkit';
import AuthSliceReducer from './auth-slice';

export const store = configureStore({
  reducer: {
  auth: AuthSliceReducer,
})

```
{% endcode %}

### Provide Store to App

Now after creating the `store` and connecting the `reducers` to it, we need to `provide` the store as an `app-wide state`.

{% code title="src/index.js" %}
```jsx
import {store} from './store/index';
import {Provider} from 'react-redux';
//...
//...

ReactDOM.render(
  <Provider store={store}>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </Provider>,
  document.getElementById('root');
);
```
{% endcode %}

### Using Actions to Login

Now we have all the things in place, it is time to use the Redux store actions provided by the `authSlice` slice.

{% code title="use-http.js" %}
```jsx
import {AuthActions} from './store/auth-slice';
import {useDispatch} from 'react-redux';
//...
const useHttp = () => {
const dispatch = useDispatch();
//...
//...
  const sendRequest = useCallback(
    async(reqConfig) => {
    //...
    try {
    //...
    //...
    dispatch(AuthActions.login(data.idToken));
    } catch(err) {/*...*/}
  }, [dispatch]);
  
  return {/*...*/}
}
```
{% endcode %}

### Verifying Login Success

After successful sign-up, we can check if the login is successful or not using the `Redux DevTool` in the browser. It gives us the app-wide auth state, which contains the current logged-in user's `authentication token`as well as the state specifying if the use has logged in, which is a boolean value \[`isLoggedIn`].

After a successful login, we can see the following changes in the Redux DevTool:

![App-wide authentication state values in the Redux DevTool.](<../../.gitbook/assets/Screenshot 2021-05-07 at 21.17.08.png>)
