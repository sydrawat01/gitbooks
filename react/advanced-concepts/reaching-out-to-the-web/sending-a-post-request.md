---
description: Sending data to back-end server or end-point API
---

# 📮 Sending a POST request

## Firebase

It is a service offered by Google, which acts as an API for our database, which isn't actually a database.

Create a project on Firebase. Select real-time database, and create a new real-time database, with test settings. This will give us an API URL to which we can send and receive data from.

To use it in thee `fetch` method in our React app, we will use the URL with a `/movies.json`, which will create a new node called `movies` , and the `.json` in the end is a standard for Google Firebase real-time db, through which we fetch and post data.

```jsx
const response = await fetch(
  'https://movie-db-default-rtdb.firebaseio.com/movies.json'
);
```

### Sending data to `POST` to server

We need to set the `method` to `POST`, and use `JSON.stringify()` to convert the JavaScript object into a JSON object so that it can be sent as the POST `body`. We also set the headers here.

Of course, we need to add the `async` and `await` keywords since "POSTing" data to the server is also an asynchronous task.

```jsx
const addMovieHandler = async(movie) => {
  const response = await fetch(
    'https://movie-db-6b0ae-default-rtdb.firebaseio.com/movies.json', {
       method: 'POST',
       body: JSON.stringify(movie),
       headers: {
         'Content-Type': 'application/json'
       }
    }
  );
}
```

Here, `movie` is an object which contains `id`, `title`, `releaseDate` and `openingText` about a movie.

Here is a screenshot of the data when we send the POST request to the API, and store the data in Firebase:

![](../../.gitbook/assets/Screenshot\_2021-04-24\_at\_14.57.38.png)

To access the data from the Firebase database, we would need to access the data on the unique keys that are added to the data by Firebase.

```jsx
const fetchMovieHandler = async() => {
  const response = await fetch('https://movie-db.firebase.io/movies.json');
  const data = await response.json();

  const loadedMovies = [];
  for (const key in data) {
    loadedMovies.push({
      id: data[key].id,
      title: data[key].title,
      releaseDate: data[key].releaseDate,
      openingText: data[key].openingText
    })
  }

  //iterate over loadedMovies with .map() and set state of movies to each movie
  //in the loadedMovies array.
}
```
