---
description: Protecting Front-end pages in our React App.
---

# 💂🏻‍♂️ Navigation Guards

## What & Why?

Navigation guards are basically used to overcome the issue of a user manually entering a route path in the browser address bar, and gaining access to that route, without authentication.

We basically need to dynamically change our `Route configuration` based on whether we are logged-in or not.

## How?

We can use the global authentication `token` and the `isLoggedIn` state values to dynamically render the routes.

{% code title="App.js" %}
```jsx
//...
import {Route, Switch, Redirect} from 'react-router-dom';
import {useSelector} from 'react-redux';
//...
const App = () => {
  const {isLoggedIn} = useSelector(state => state.auth);
  
  return(
  <Layout>
    <Switch>
      <Route path="/" exact>
        <HomePage />
      </Route>
      {!isLoggedIn && (
        <Route path="/auth">
          <AuthPage />
        </Route>
      )}
      <Route path="/profile">
        {isLoggedIn && <UserProfile />}
        {!isLoggedIn && <Redirect to="/auth" />}
      </Route>
      <Route path="*">
        <Redirect to="/" />
      </Route>
    </Switch>
  </Layout>
  );
}
```
{% endcode %}

With this setup in place, the user who is not logged in, has no access to the route `/profile` and is **automatically re-directed** to `/auth` route, and the use who is logged-in, has no access to the `/auth` route.
