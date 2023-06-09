---
description: Working with fetch() and Axios to work with APIs.
---

# 🌐 Reaching out to the Web

## Browser side apps do not directly talk to Databases

![Front-end and back-end interaction.](<../../.gitbook/assets/Screenshot 2021-05-20 at 10.10.38.png>)

## HTTP requests in React

Typically, in a standalone React app, when we request data from the server, we do not get an HTML page from the server, we get JSON data back from it, which we in turn render on the web app.

## `Axios`

This is a `Promise` based `XMLHTTPRequest()` wrapper, which we can include in our project using the following command:

```bash
yarn add axios
```

### GET request

Let's create an HTTP GET request to fetch data from the server. Here, we will be using a dummy API from [JSONPlaceholder](https://jsonplaceholder.typicode.com/), where we access the `/posts/` route on this website to access all the posts available on this dummy JSON data website.

We will access the posts from this URL: [https://jsonplaceholder.typicode.com/posts/](https://jsonplaceholder.typicode.com/posts/)

During component creation, Ii the lifecycle hooks for class based components, we will access the server in the `componentDidMount()` lifecycle hook, because this is where we can cause side effect, like making HTTP requests.

We also need to import `axios`.

{% code title="Blog.js" %}
```jsx
import axios from 'axios'
import React, {Component} from 'react'
import Post from '....'
import FullPost from '....'
import NewPost from '....'
import classes from '....module.css'

class Blog extends Component {
  state = {
    posts: [],
    selectedPostId: null
  }
  componentDidMount() {
    axios.get('https://jsonplaceholder.typicode.com/posts')
        .then(response => console.log(response))
  }
```
{% endcode %}

We can see in the console, the response object that is returned by the URL. We can now render this into out Post component, with limiting the number of posts from the data, and adding our own attribute to the data as well (appending data to response data). We'll also add a `clickEventHandler` to select a post from the displayed 4 posts from the server, and show the title and content on the `FullPost` component.

```jsx
componentDidMount() {
  axios.get('https://jsonplaceholder.typicode.com/posts')
    .then((response) => {
    const posts = response.data.slice(0,4)
    const updatedPosts = posts.map(post => {
      return {
        ...post,
        author: 'Sid'
      }
    })
    this.setState({posts: updatedPosts})
     })
}

//...
//...
//selected Post event click handler
postSelectedHandler = (id) => {
  this.setState({ selecedPostId: id })
}

render() {
  const posts = this.state.posts.map(post => (
    <Post 
       key={post.id}
       title={post.title}
       author={post.author} 
       clicked={()=>this.postSelectedHandler(post.id)}
    />
  ))
  return(
    <div>
      <section className={classes.Posts}>
    {posts}
      </section>
      <section>
    <FullPost id={this.state.postSelectedId}/>
      </section>
      <section>
    <NewPost />
      </section>
    </div>
  )
}
```

Let's see our `Post` component:

{% code title="Post.js" %}
```jsx
import React from 'react'
import classes from '....module.css'

const Post = ({title, author, clicked}) => {
  return(
    <article onClick={clicked}>
      <h1>{title}</h1>
      <div className={classes.Info}>
    <div className={classes.author}>
      {author}
    </div>
      </div>
    </article>
  )
}
```
{% endcode %}

In our `FullPost` component, we need to display the selected post based on the `selectedPostId`:

**NOTE**: Since we are updating the `FullPost` component upon a click event from the `Post` component, we need to fetch the post data inside a lifecycle hook function where we can perform side effects like accessing the server. So the HTTP request to the server will come from the `componentDidUpdate()` lifecycle hook during the component update lifecycle.

{% code title="FullPost.js" %}
```jsx
import React, { Component } from 'react'
import axios from 'axios'

class FullPost extends Component {
  state = {
    loadedPost: null
  }
  componentDidUpdate() {
    axios.get('https://jsonplaceholder.typicode.com/posts/' + this.props.id)
      .then(response => this.setState({loadedPost: response.data}))
  }
}
```
{% endcode %}

Here, we will have to fetch data on update, without creating infinite loops! If we were to check the network tab when we click a post, there are continuous requests being made to the server since we are updating or setting the state inside the `componentDidUpdate` lifecycle hook. To overcome this, we need to add a condition to only fetch data and update the state when the `loadedPost` is null or when the `id` of the displayed post is not equal to the `id` of the post that is selected. We would also need a check if the `this.props.id` is valid, i.e, if a post is even selected firstly!

{% code title="FullPost.js" %}
```jsx
//...
//...
componentDidUpdate() {
  if (this.props.id) {
    if (
      !this.state.loadedPost || 
      (this.state.loadedPost && this.state.loadedPost.id !== this.props.id)) {
        axios.get('https://jsonplaceholder.typicode.com/posts/' + this.props.id)
          .then(response => this.setState({loadedPost: response.data}))
    }
  }
}
```
{% endcode %}

Now, we need to conditionally render the post data that is selected.

{% code title="FullPost.js" %}
```jsx
//...
//...
render() {
  const {id} = this.props
  let post = <p style={{textAlign: 'center'}}>Please select a post!</p>
  if (id) post = <p style={{textAlign: 'center'}}>Loading...!</p>
  if (this.state.loadedPost) {
    post = (
      <div className={classes.FullPost}>
    <h1>{this.state.loadedPost.title}</h1>
    <p>{this.state.loadedPost.body}</p>
    <div className={classes.Edit}>
      <button onClick={this.deleteDataHandler}>Delete</button>
    </div>
      </div>
    )
  }
  return post;
}
```
{% endcode %}

With this, the requests to the server are limited, and the post is displayed only when the post is not already loaded, or when the `id` of the displayed and selected posts do not match. The highlighted part in the above code, is a `delete` button, which will help us to delete data from the dummy server, let see how we can do that.

### DELETE request

In the above code, we have added a button `delete` to delete the post based on the `FullPost` that is displayed. So we need to send a `DELETE` request to the server to remove the entry from the back-end. Since we are working with a dummy back-end server, we will only `console.log` the deleted request.

{% code title="FullPost.js" %}
```jsx
//...
//...
deleteDataHandler = () => {
  axios.delete('https://jsonplaceholder.typicode.com/posts/' + this.props.id)
    .then(response => console.log(response))
}
```
{% endcode %}

### POST request

To POST data to the server, we can send a POST request to the URL with axios. Let's POST some data to the server in the `NewPost` component:

{% code title="NewPost.js" %}
```jsx
import React, {Component} from 'react'
import classes from '....module.css'
import axios from 'axios'

class NewPost extends Component {
  state = {
    title: '',
    content: '',
    author: 'Sid'
  }
  render() {
    return (
      <div className={classes.NewPost}>
        <h1>Add a post</h1>
        <label>Title</label>
        <input
          type="text"
          value={this.state.title}
          onChange={(e) => this.setState({ title: e.target.value })}
        />
        <label>Content</label>
        <textarea
          rows="4"
          value={this.state.content}
          onChange={(e) => this.setState({ content: e.target.value })}
        ></textarea>
        <label>Author</label>
        <select
          value={this.state.author}
          onChange={(e) => this.setState({ author: e.target.value })}
        >
          <option value="Sid">Sid</option>
          <option value="Max">Max</option>
        </select>
        <button onClick={this.postDataHandler}>Add Post</button>
      </div>
    );
  }
}
export default NewPost
```
{% endcode %}

We add the `postDataHandler` to the `Add Post` button to POST the data to the dummy server, and log the result to the console. Since this is a dummy POST entry to the server, we can only log the response.

{% code title="NewPost.js" %}
```jsx
//...
//...
postDataHandler = () => {
  const data = {
    title: this.state.title,
    content: this.state.content,
    author: this.state.author
  }
  axios.post('https://jsonplaceholder.typicode.com/posts/', data)
    .then(response => console.log(response)
}
```
{% endcode %}

### Handling Errors Locally

After the `then()` in the axios chain, we can add another method to this chain, called `catch()`, to catch errors that may be present while fetching or posting or deleting or performing any other activity on the server side through the URL.

Let's see how it would work if we had an error in out `GET` request:

{% code title="Blog.js" %}
```jsx
//...
//...
class Blog extends Component {
  componentDidMount() {
    axios
      .get('https://jsonplaceholder.typicode.com/postsss')
      .then((response) => {
        const posts = response.data.slice(0, 4);
        const updatedPosts = posts.map((post) => {
          return {
            ...post,
            author: 'Sid',
          };
        });
        this.setState({ posts: updatedPosts });
      })
      .catch((e) => console.log(e);
  }
}
```
{% endcode %}

We can also not log the error, and use the catch block to show something on the webpage in case there is an error.

{% code title="Blog.js" %}
```jsx
//...
//...
class Blog extends Component {
  state = {
    //...
    //...
    error: false
  }
  componentDidMount() {
    //...
    //...
    .catch((e) => this.setState({error: true}))
  }
  render() {
    let posts = <p style={{textAlign: 'center'}}>SOMETHING WENT WRONG!</p>
    if(!this.state.error) {
      posts = this.state.posts.map({/*...*/}
      <Post {*/...*/}/>
        )
    //...
    //...
    }
    return (
      <div>
    <section className={classes.Posts}>{posts}</section>
    //...
    //...
    //...
      </div>
    )
  }
}
```
{% endcode %}

## Interceptors

Axios has interceptors, to execute code globally, or usually to handle errors on a global level, rather than a local level as we saw above with the `catch` method chaining. This is the place where we add authentication headers and such other things in a web application.

We will add the `interceptors` in the `index.js` file,

#### Request Interceptor

{% code title="index.js" %}
```jsx
//...
import axios from 'axios'

axios.interceptors.request.use((request) => {
  console.log(request)
  //edit the request, eg: add auth headers here, etc.
  return request
}, (error) => {
  console.log(error)
  return Promise.reject(error)
})
```
{% endcode %}

In the `request` method of interceptors, we need to always return the request data, otherwise we are blocking the request! In case there is an error, we need to reject to `Promise` with that error, as shown above in the code snippet.

#### Response Interceptor

{% code title="index.js" %}
```jsx
//...
axios.interceptors.response.use((response) => {
  console.log(response)
  //edit the response here
  return response
}, (error) => {
  console.log(error)
  return Promise.reject(error)
})

ReactDOM.render(
  <App />, document.getElementById('root')
)
```
{% endcode %}

### Removing Interceptors

You learned how to add an interceptor, getting rid of one is also easy. Simply store the reference to the interceptor in a variable and call `eject` with that reference as an argument, to remove it (more info: [https://github.com/axios/axios#interceptors](https://github.com/axios/axios#interceptors)):

```jsx
let myInterceptor = axios.interceptors.request.use((request) => {/*...*/});

axios.interceptors.request.eject(myInterceptor);
```

### Setting global config using Interceptors

We will use the `defaults` object to set up default config for all the requests being sent out. There we can use a `baseURL` property to set the base URL. The other paths where we use `axios.get()` or `axios.post()` or `axios.delete()` will now have the `baseURL` appended to their paths.

{% code title="index.js" %}
```jsx
//...
//...
axios.defaults.baseURL = 'https://jsonplaceholder.typicode.com'
```
{% endcode %}

We can also do this with `headers` as well.

```jsx
axios.defaults.headers.common['Authorization'] = 'AUTH TOKEN'
axios.defaults.headers.post['Content-Type'] = 'application/json'
```

So now when we POST some data to our dummy server, we can see in the logged response object, the above headers.

![Response object logged to the console.](../../.gitbook/assets/Screenshot\_2021-03-28\_at\_12.41.16.png)

In the above screenshot, we can see under `headers`, _"Authorization: AUTH TOKEN"_ and _"Content-Type: application/json"_

### Creating and using Axios `Instances`

We can use Axios Instances in places where we want to override or use our own configurations instead of the default global ones setup in the `index.js` file.

To create an Axios Instance, we will create a new file called `Axios.js` in the `src/` folder:

{% code title="src/Axios.js" %}
```jsx
import axios from 'axios'

const instance = axios.create({
  baseURL: 'https://jsonplaceholder.typicode.com'
})

instance.defaults.headers.common['Authorization'] = 'AUTH TOKEN FROM INSTANCE'

export default instance
```
{% endcode %}

Now, we can use this in any of our components, say `Blog.js`:

{% code title="Blog.js" %}
```jsx
//...
//import axios from 'axios'
import axios from '../../Axios'
```
{% endcode %}

We can also define the interceptors for our instances as well.

```jsx
instance.interceptors.request.use....
```

Using `Instances` allows us to have the flexibility to control, in detail, in which part of the app we want to use the default settings.

For more info on `Axios`, check out their [official docs](https://github.com/axios/axios).

## `Fetch` API

This is a feature built into the browser for fetching data from an API. It is an alternative to `Axios`

### Syntax

```jsx
fetch('url://string/to/api', {
    key: value pairs to configure options, headers, https method, etc.
  })
```

We can avoid the second parameter, which is the object with key-value pairs, that tells what config is needed to fetch data from the server. The default `method` if we do not provide this second parameter, is `GET`, to get data from the server. In case we need to send data to the server, we will use the second parameter, with the key-value pair of `method: 'POST'`. The `JSON` object that we need to POST to the server is sent in the `body` key, with the `JSON` object as the value.

Fetch returns a promise, that needs to be handled appropriately. This is an asynchronous process, and hence we have promises.

So whenever we get a response, we have a `then()` to handle the response. To handle errors, we use the `catch()`.

{% code title="App.js" %}
```jsx
import React, { useState } from 'react';

const App = () => {
  const [movies, setMovies] = useState([]);

  const fetchMoviesHandler = () => {
   fetch('https://swapi.dev/api/films')
      .then(response => response.json())
      .then(data => {
      const fetchedMovies = data.results.map(movie => {
        return {
          id: movie.episode_id,
          title: movie.title,
          releaseDate: movie.release_date,
          openingText: movie.opening_crawl
        }
      })
      setMovies((prevState) => fetchedMovies)
        })
      .catch() 
  }

  return (
   <>
     <button onClick={fetchMoviesHandler}>Fetch Movies</button>
     <MoviesList movies={movies} />
   </>
  )
}
```
{% endcode %}

Since the `fetch()` works with promises, we have to chain the `then()` blocks after it. This `then()` block chaining would become a really long chain if we have a lot of `then()` blocks. To avoid then block chaining, we have something called `async` and `await`.

### `async` & `await`

We add the `async` keyword in front of the function where we perform a side-effect, like fetching data from a server, which is an asynchronous task. Then, we add the `await` keyword in front of the actual method that performs the side-effect, i.e, where we call the `fetch()` function to fetch data from the server.

The `async` and `await` work in the exact way the `fetch()` works with the `then()` method calls. This would simplify our code to a good extent.

```jsx
const fetchMoviesHandler = async() => {
  const response = await fetch('https://swapi.dev/api/films');
  const data = await response.json();

  const fetchedMovies = data.results.map(movie => {
    return {
      id: movie.episode_id,
      title: movie.title,
      releaseDate: movie.release_date,
      openingText: movie.opening_crawl
    }
  })
  setMovies((prevState) => fetchedMovies)
}
```
