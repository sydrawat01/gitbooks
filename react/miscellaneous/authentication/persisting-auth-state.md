---
description: User remains / stays logged in to our app.
---

# 🔐 Persisting Auth State

When we reload the browser tab, we lose the state of authentication for the user that has logged in. The sign-in with email API endpoint provides a time limit till which the user will stay logged in, which comes in the response payload body as `expiresIn` variable, which states the amount of time in seconds, the currently logged in user will stay logged in.

After the `expiresIn` times out, Firebase has to create a new token, which is done using the `refreshToken` variable, which creates a fresh authentication token for the current user, who's session is about to expire. But this is a little bit too advanced, so we will stick to using the `expiresIn` variable to keep track of the currently logged in user.

## Storing Auth Token

We cannot store the authentication token in our app, since, when our app reloads, or when the page is reloaded, this state is reset. It does not persist in memory. For this reason, we need to store our authentication token either in `localStorage` or in `cookies`. To learn more about these two storage options and what & why we use them, check out [this blog and the video](https://academind.com/tutorials/localstorage-vs-cookies-xss/).

### Using Local Storage

We will be using `localStorage` to save our authentication token for the current user. This is a storage that is built-in to the browser that helps us store small data which then persists in memory even when our page reloads.

{% code title="auth-slice.js" %}
```jsx
import {createSlice} from '@reduxjs/toolkit';

const initialState = {
  token = localStorage.getItem('token'),
  isLoggedIn = !!localStorage.getItem('token')
}

const authSlice = createSlice({
  name: 'auth',
  initialState,
  reducers: {
    login(state, action) {
      state.token = action.payload.idToken;
      state.isLoggedIn = true;
      localStorage.setItem('token', action.payload.idToken);
    },
    logout(state) {
      state.token = null;
      state.isLoggedIn = false;
      localStorage.removeItem('token');
    },
  },
});

export default authSlice;
```
{% endcode %}
