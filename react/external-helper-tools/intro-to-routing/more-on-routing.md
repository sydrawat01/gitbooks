---
description: A more deeper dive into routing in React apps.
---

# 🗺 More on Routing

## Handling default case in `Switch`

In the `switch` component, where we match for the path URL, if we do not find any match, we should display a `404: page not found` page. It is very simple to add this. All we need to do is add a `Route` as the last item inside the `Switch` component:

```jsx
<Switch>
  //...
  //...
  //... 

  //...
  <Route path="*">
    <NotFoundComponentPage />
  </Route>
</Switch>
```

In the path of this last `Route`, we check for `*` , which means any other path apart from the ones defined above it, will match with this, and hence route the app to the `NotFoundComponentPage`.

## Imperative \[_programmatic_] Navigation

Triggering a navigation action and navigation the user away programmatically is called imperative navigation.

Suppose, in our app, we have a form that submits some form data to the server. After successful submission of data to the server, we want the user to be re-directed to another `route` in our app, we would have to do this using `imperative navigation`.

So it is not a `Link` that would navigate the use away from that form submission page, but an `action` that would take the user to the desired `route`.

For this, we use the `useHistory` hook provided by the `react-router-dom` package. This hook allows us to change the browser history. It returns a history object, which we can then use to trigger some history changing actions.

So what changes the history of pages? Adding a new page or going to a new page! The following methods on the `history` object (returned by the `useHistory` hook):

* We can navigate around using the `push` method → pushes a new page to the stack of pages, i.e, a new page in our history of pages.
* We can navigate using the `replace` method → replaces the current page.

The difference between the two is that with the `push` method, we can go back using the back button on the browser to the page we're coming from, which is not possible in the `replace` method.

```jsx
import {useHistory} from 'react-router-dom';
//...
//...
const history = useHistory();
//code where the event handler is present
const addQuoteHandler = (quoteData) => {
  console.log('pushing data to server: ', quoteData);
  history.push('/quotes');
}
```

## `Prompt` component

To prevent unwanted route transitions, we use the `Prompt` component provided by the `react-router-dom` package. This is when we are filling data into a form and we accidentally go back to the previous page, and when we come back to the form, all the details that we filled in the form are lost. To prevent this from happening, web pages issue a `prompt` when the user is about to leave or navigate away from the current page.

The `Prompt` component will show a prompt to the user when they try to navigate away from the page. It takes in two parameters:

* `when` to specify when the prompt should be shown.
* `message` which takes in an anonymous function, which returns a string, and has a `location` parameter in this function which gives us info about the page the use is trying to navigate to/from.

```jsx
import {Prompt} from 'react-router-dom';
import {useState} from 'react';

//...
//...
  const [isEntering, setIsEntering] = useState(false);
  //...
  //...
  const focusFormHandler = () => {
    setIsEntering(true);
  }
  const finishListeningHandler = () => {
    setEntering(false); //prevent Prompt to show up after successful form submission
  }

  const formSubmitHandler = (event) => {
    event.preventDefault();
    //...
    //...
    props.onAddQuote({/*...*/});
  }

  return(
    <>
      <Prompt 
    when={isEntering}
    message={(location) => 'Are you sure you want to leave this page?'}
      />
      <form onSubmit={formSubmitHandler} onFocus={focusFormHandler}>
        //...
        //...
        //...
        <button onClick={finishListeningHandler}>Add Quote</button>
      </form>
    </>
  )
```

## Query parameters

Path parameters are mandatory, whereas query parameters are optional. The query parameters pass extra details to the page.

The `useHistory` hook give us access to the history object, an object that allows us to change and manage the URL. The `useLocation` hook is also provided by the `react-router-dom` package, which gives us access to the location object, which has the details of the currently loaded page.

General structure of the location object:

![Query parameter object.](../../.gitbook/assets/Screenshot\_2021-05-01\_at\_12.03.12.png)

Clicking on sort button should re-render the page with the appropriate sorting method i.e, ascending or descending.

```jsx
import {useHistory, useLocation} from 'react-router-dom';
//...
//...
const history = useHistory(); //change or manage URL
const location = useLocation(); //details of currently loaded page
const changeSortHandler = () => {
  history.push('/quotes?sort=asc')
}

//...
//...
//...
  <buttin onClick={changeSortHandler}>Sort Ascending</button>
```

The `URLSearchParams()` is a built in constructor function in the browser, which returns an object. We can use this in the browser, and it takes in the location object property, and the returned object can be used to extract the query parameters by key.

We use the `.get()` method on the `queryObject` returned by the `URLSearchParams()` constructor function.

```jsx
//...
//...
const location = useLocation();
const queryParams = new URLSearchParams(location.search);

const isAscending = queryParams.get('sort') === 'asc'; // returns 'asc' and sets to true
```

We can then use these values to sort our data and re-render the component whenever the button is clicked, i.e. the URL is changed via `useHistory` hook, and thus get the sorted component children.

```jsx
//...
import {useHistory, useLocation} from 'react-router-dom';
//...

const sortQuotes = (quotes, ascending) => {
  return quotes.sort((quoteA, quoteB) => {
    if(ascending) {
      return quoteA.id > quoteB.id ? 1 : -1;
    } else {
      return quoteA.id < quoteB.id ? 1 : -1;
    }
  })
}

const QuoteList = () => {
  const history = useHistory();
  const location = useLocation();
  const queryParams = new URLSearchParams(location.search);
  const isAscending = queryParams.get('sort') === 'asc;

  const changeSortHandler = () => {
    history.push(`/quotes?sort=${isAscending ? 'desc' : 'asc'}`);
  }

  //...
  //...
  return(
    <>
      <button onClick={changeSortHandler}>
      Sort ${isAscending ? 'Descending' : 'Ascending'}
      </button>
      //...
      //...
    </>
  )
}
```

## Conditional rendering using nested routes

Suppose we want to show a `Link` only when the route is `/quotes/:quoteId` and make it disappear when the `Link` is clicked and the page is routed to `/quotes/:quoteId/comments` . In this case we can use nested routes to conditionally render the `Link` component on the page.

```jsx
//...
//...
<>
  <HighlightedQuote id={id} author={author} text={text}/>

  <Route path={`/quotes/${params.quoteId}` exact}>
    <Link to={`/quotes/${params.quoteId}/comments`}>
      Show Comments
    </Link>
  </Route>

  <Route path={`/quotes/${params.quoteId}/comments`}>
    <Comments/>
  </Route>
</>
```

## More flexible routing code: `useRouteMatch`

Suppose we want to change the base path from `/quotes` to `/quote` . We would have to change this everywhere across our application manually. It is easier on the `App` component, but gets more problematic when there are nested routes. Even more problematic with deeply nested routes.

To overcome this issue, `react-router-dom` provides us with another hook called `useRouteMatch`, which is similar to the `useLocation` hook, but contains more information about the currently loaded `Route`.

This is how the object that `useRouteMatch` returns looks like:

```jsx
import {useRouteMatch} from 'react-router-dom';
//...
//...
  const match = useRouteMatch();
  console.log(match);
```

![useRouteMatch() hook result object.](../../.gitbook/assets/Screenshot\_2021-05-01\_at\_13.17.12.png)

We can use this `matchObject` which is returned by the `useRouteMatch` hook to construct our nested routes dynamically, instead of hard-coding the values to the `path`.

```jsx
import {useLocation, useRouteMatch} from 'react-router-dom';

const match = useRouteMatch();
const location = useLocation();
console.log(location); // location object
console.log(match); //match object, screenshot above
```

`useLocation` object :

![useLocation() hook result object.](../../.gitbook/assets/Screenshot\_2021-05-01\_at\_13.28.58.png)

So we can use `match.path` or `match.url` OR `location.pathname` to get the paths dynamically.

## Complex URL paths

Strings are usually how we define our path in the `Route` components. But in cases where we are dealing with query parameters in our path, the path string might get too long or too big, and thus will be unreadable. To avoid such cases, we can also opt for objects are path values.

This object has the following properties which makes our route path more readable:

* `pathname` : The path to which we want to route to.
* `search`: This allows us to add query parameters.

```jsx
import {useHistory, useLocation} from 'react-router-dom';

const history = useHistory();
const location = useLocation();
//...
  history.push({
    pathname: location.pathname,
    search: `?sort=${isAscending ? 'desc' : 'asc'}`
  })
//history.push(`${location.pathname}?sort=${isAscending ? 'desc' : 'asc'}`);
```
