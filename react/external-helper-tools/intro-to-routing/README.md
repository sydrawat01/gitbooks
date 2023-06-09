# 🚏 Intro to Routing

## What is Routing?

Routing is multi-page feeling in a single page app.

![SPA with routing.](../../.gitbook/assets/Screenshot\_2021-04-15\_at\_19.38.53.png)

Routing is about being able to show different pages to the user.

An SPA is a single page, a single HTML file. We still want to provide the user with a normal web using experience though, we want to show the user different pages for different URLs. The trick just is that we don't actually have multiple HTML files, but then we instead use JavaScript to render different pages for different paths.

So we don't really have different files but simply we re-render parts off that single page or maybe the entire single page depending on which path the user navigated to in our application. This is what routing is about, parsing this path, so the path after our domain and showing the appropriate JSX or component code in our app. For that, we're going to use a router package to add such a functionality so that we don't have to parse that path on our own which is non-trivial. That router package has a couple of tasks:

* To parse the URL path to understand where the user wanted to go to.
* We have to configure different paths in our application which we support and the router package can then read our configuration basically, so that it knows which paths are supported and what should happen when the user visits one of these paths.
* It will then render or load the appropriate JSX or component code depending on which path the user visited.

This is the idea behind routing, load different code, conditional JSX or component code for different paths and we use a router package so that we don't have to determine which path the user is on on our own.

![Core idea of a SPA.](<../../.gitbook/assets/Screenshot 2021-05-20 at 12.05.33.png>)

## `react-router` vs `react-router-dom`

We installed both `react-router` and `react-router-dom` . **Technically, only `react-router-dom` is required for web development**. It wraps `react-router` and therefore uses it as a dependency.

### Basic setup with `react-router-dom`

#### Defining and using routes

Installing the React router dependencies:

```bash
yarn add react-router-dom
```

Once the installation of the package is complete, we can then wrap our `App` with the `Route` from the `react-router-dom` package, to render the component \[or pages now] conditionally, i.e, when the user changes the URL to `/welcome` or `/products` :

{% code title="App.js" %}
```jsx
import React from 'react'
import {Route} from 'react-router-dom'
import Welcome from './pages/Welcome'
import Products from './pages/Products'

const App = () => {
  return (
    <div>
      <Route path="/welcome">
        <Welcome />
      </Route>
      <Router path="/products">
        <Products />
      </Route>
    </div>
  )
}

export default App
```
{% endcode %}

Next, we need to wrap our `App` with `BrowserRouter` in the `index.js` so that we can activate these routes!

```jsx
//...
import {BrowserRouter} from 'react-router-dom'
//...

ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  document.getElementById('root')
);
```

With this, we can now use routes throughout our application! When we go to `http://URL/welcome` , we can see the `Welcome` component \[or rather the page] being rendered, and the same for the `Prodcuts` page.

### Working with links

We can set up our app the go to the specified route when we click on a link. But when we add the route link to an `anchor` HTML element, i.e, the `<a href="/welcome">Welcome</a>` tag, the page sends a new request to the server for a brand new HTML page, so the whole app refreshes, which we do not want! _\[We might lose application state of we do this, which is against the idea of a SPA]_

To overcome this problem, the `react-router-dom` package has a `Link` component. It allows us to create a link in the end. These `Link` components are in the end, rendering `anchor` tags, but also have a click listener, which prevents the browser default of reloading the page, and routing to the specified route in the `to` prop.

{% code title="Header.js" %}
```jsx
import {Link} from 'react-router-dom';

const Header = () => {
  return (
    <div>
      <ul>
    <li>
      <Link to="/welcome">Welcome</Link>
    </li>
    <li>
      <Link to="/products">Products</Link>
    </li>
      </ul>
    </div>
  )
}

export default Header;
```
{% endcode %}

#### Using `NavLinks`

If we want the current active link to be highlighted in our `nav` or navigation header component, we can use another component available in the `react-router-dom` package, called `NavLink`. It works in the same way the `Link` component used to work, but does a bit more than that. The `NavLink` will also set an active css class on an active item, by adding the `activeClassName` prop to it.

```jsx
import {NavLink} from 'react-router-dom';
import classes from './Header.module.css';
//...
  //...
    //...
      <ul>
    <li>
      <NavLink activeClassName={classes.active} to="/welcome">Welcome</NavLink>
    </li>
    <li>
      <NavLink activeClassName={classes.active} to="/products">Products</NavLink>
    </li>
      </ul>
```

This is a nice feature to have, when we have routing on our SPA. This is simple and very easy to implement!

### Dynamic routes with `params`

To add dynamic routes, we have a special syntax:

```jsx
<Route path="/product-detail/:productId">
  <ProductDetails />
</Route>
```

The `colon` or `:` is the special character that we need to add to our path, and the `productId` is an object, a `param` that we have with a key and value pair, which we can extract and work with, within our component.

#### `useParams` hook

{% code title="ProductDetails.js" %}
```jsx
import {useParams} from 'react-router-dom';
const ProductDetails = () => {
  const params = useParams();

  return(
    <>
      <h1>Product details</h1>
      <p>{params.productId}</p>
    </>
  );
}
```
{% endcode %}

We can then use the `useParams` hook provided by the `react-router-dom` package, which helps us extract the `params` object that we pass \[a dynamic route object], to access the path.

### Configuring routes

If we have a list of items on a route, we need to append these to the same root route, just like adding sub-directories in a folder. Suppose we have `/products` route, and have subsequent products `p1`, `p2` and `p3`. To add these to the root `/products` to achieve something like `/products/p1` or `/products/p2` or `products/p3` instead of creating new routes for these products, we use something called `switch` and `exact`.

#### `Switch`

The `Switch` component is provided by `react-router-dom` to wrap out `Route` components with. React router reads our routes from top to bottom and renders the component that matched the immediate path, and not the entire path.

This does basically what a `switch` generally does, it selects one route and renders that component which matched with the route `path`. It does not match the whole `path`. So problem here is we do not render the sub-directories, if they are written below the main directory, as in the example below:

```jsx
import {Route, Switch} from 'react-router-dom';
//...
<Switch>
  <Route path="/welcome"> <Welcome /> </Route>
  <Route path="/products"> <Products /> </Route>
  <Route path="/products/:productId"> <ProdcutDetails /> </Route>
<Switch>
```

Here, since the path `/products` is before `products.:productId`, the `Switch` component matched with this first instance and renders that path and it's respective component.

In simple terms, even if the URL path is `/products/p1`, or `p2` or `p3`, it will only render till `/prodcut` because the `Switch` component does not do strict matching.

#### `Exact`

To over come the above problem and to check the complete path, rather than just the initial path value, we use a special keyword `exact`. This is sent as a prop to the `Route` component. This tells the route that it should lead to a match only if it has an exact match!

```jsx
import {Route, Switch} from 'react-router-dom';
//...
<Switch>
  <Route path="/welcome"> <Welcome /> </Route>
  <Route path="/products" exact> <Products /> </Route>
  <Route path="/products/:productId"> <ProdcutDetails /> </Route>
<Switch>
```

This will only render the `Products` component if the URL has only `/products` in it's path.

### Nested routes

We are not limited to defining routes in one place only. We can define our routes anywhere. If they are on the component which is currently active, the `Route` will be re-evaluated by `react-router-dom`.

{% code title="Welcome.js" %}
```jsx
import {Route} from 'react-router-dom';
//...
//...
  <Route path="/welcome/new-user">
    <p>Welcome, new user!</p>
  </Route>
```
{% endcode %}

So with this, we add a nested route called `/welcome/new-user` within the main route `/welcome`.

The paragraph "_Welcome, new user!_" will only be shown when the user visits the `/welcome/new-user` path, and will not be shown on the `/welcome` path.

### Redirects

This is a feature that allows us to redirect the user. This is in the case the user goes to a route that does not exist. Say for example, we have routes for `/welcome`, `/products` and `/products/:productId` and the user goes to `/nothing`. This will not send the user to a `404` page, and only display a blank page. This is not the behavior we want. So we have to `redirect` our user to either a `404` page, or back to the `welcome` page.

In the redirect route, we render a component provided by `react-router-dom`, called `Redirect`. It does what it's name suggests, it redirects the user to another route in the app, which is passed as a prop to the `to` property in this component.

```jsx
import {Route, Switch, Redirect} from 'react-router-dom';
//...
//...
  <Route path="/" exact> <Redirect to="/welcome" /> </Route>
```

With this, we can redirect the user `from` any route `to` any route!
