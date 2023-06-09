---
description: >-
  Showing spinners when data is loading, and errors in case the request fails or
  there is any other problem fetching data from and sending data to the server
  or API end-point.
---

# ⚠️ Errors & Handling Loading

## Loading of data

When we fetch data from a server, it does take some time to respond back with the data, so in the mean time, we would prefer to show a spinner on the font, while the app is fetching data from the server in the back.

We can add spinners in such cases to display that our app is working on getting the data (fetching) from the back-end server. We can get the spinner `CSS` code from [here](https://projects.lukehaas.me/css-loaders/).

We can manage a state to show a loading spinner or not.

{% code title="App.js" %}
```jsx
const [isLoading, setIsLoading] = useState(false);

function App() {
  const [movies, setMovies] = useState([]);
  const [isLoading, setIsLoading] = useState(false);

  const fetchMoviesHandler = async() => {
    setIsLoading(true);
    const response = await fetch('https://swapi.dev/api/films');
    const data = await response.json();

    const fetchedMovies = data.results.map((movie) => {
      return {
        id: movie.episode_id,
        title: movie.title,
        releaseDate: movie.release_date,
        openingText: movie.opening_crawl,
      };
    });
    setMovies((prevState) => fetchedMovies);
    setIsLoading(false);
  }

  return (
    <React.Fragment>
      <section>
        <button onClick={fetchMoviesHandler}>Fetch Movies</button>
      </section>
      <section>
        {!isLoading && movies.length > 0 && <MoviesList movies={movies} />}
        {!isLoading && movies.length === 0 && <p>No movies found!</p>}
        {isLoading && <p>Loading ...</p>}
      </section>
    </React.Fragment>
  );
}
```
{% endcode %}

## Handling errors

In case we get connect to the server, we get a response 2XX, which means successful connection established, but at times when we have to receive data back from the server, we might get a 4XX response code, which may mean a bunch of things, which usually means something is wrong. [More on errors codes here.](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)

We need to handle such scenarios in our app, i.e, error handling.

If we are using the `fetch().then()`block, we have the option to chain the `catch()` block after the `then()` chains, and we can handle the errors appropriately in this `catch()` block.

In case we use the `async` and `await` keywords to fetch data from the server, we need to use the `try-catch` block. We also need to `throw` an error from the try block in case we encounter a problem while `fetching` data from the server.

```jsx
  const [error, setError] = useState(null);

  const fetchMoviesHandler = async() => {
    setIsLoading(true);
    setError(null);
    try {
      const response = await fetch('https://swapi.dev/api/films');
      if (!response.ok) {
        throw new Error('Something went wrong');
      }
      const data = await response.json();

      const fetchedMovies = data.results.map((movie) => {
        return {
          id: movie.episode_id,
          title: movie.title,
          releaseDate: movie.release_date,
          openingText: movie.opening_crawl,
        };
      });
      setMovies((prevState) => fetchedMovies);
    } catch (error) {
      setError(error.message);
    }
    setIsLoading(false);
  }
```

We need to check for the `response` object, if `ok` is set to `true` or `false` before we parse the `response` object using `response.json()`, otherwise, the error that we throw is not set, and the error in the response object is set as the error.

### `useEffect()` for Initialization

We will wrap our `fetchMoviesHandler()` with the `useCallback()` hook so that it is not re-created when our parent component re-evaluates and re-renders. We will use the `useEffect()` hook to render the data onto the `MoviesList` component when the function `fetchMoviesHandler` changes, i.e, we add the function to the dependencies list.

```jsx
const fetchMoviesHandler = useCallback(async() => {
  //...
  //...
}, []);

useEffect(()=> {
  fetchMoviesHandler();
}, [fetchMoviesHandler]);
```
