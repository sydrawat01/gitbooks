---
description: Problems faced in testing asynchronous code, and how to solve them.
---

# 🥽 Testing Asynchronous Code

## Problem

Consider we have the following component in our app that renders a list of posts:

{% code title="Async.js" %}
```jsx
import {useState, useEffect} from 'react';

const Async = () => {
  const [posts, setPosts] = useState([]);
  
  useEffect(() => {
    fetch('https://jsonplaceholder.typicode.com/posts')
      .then(response => response.json())
      .then(data => {
        setPosts(data)
      });
  }, []);
  
  return (
    <div>
      <ul>
        {posts.map(post => <li key={post.id}>{post.title}</li>)}
      </ul>
    </div>
  );
}

export default Async;
```
{% endcode %}

Now in our test suite for Async component we want to test whether or not we have all the test list items. We use the following test code:

**NOTE:** The role for list items are `listitem`, as defined [here](https://www.w3.org/TR/html-aria/#docconformance).

{% code title="Async.test.js" %}
```jsx
import {render, screen} from '@testing-library/react';
import Async from './Async';

describe('Async Component Test Suite', () => {
  test('Renders posts if request succeeds', () => {
    render(<Async />);
    const posts = screen.getAllByRole('listitem');
    expect(posts).not.toHaveLength(0);
  });
});
```
{% endcode %}

By looking at the test code, it looks as our test should pass without any problems, but it is not the case!

### Test Failure

When we run the above test suite, we end up with the test failing, with the following result:

![Async test suite failure result.](<../../.gitbook/assets/Screenshot 2021-05-12 at 22.22.46.png>)

We see that the error states the `listitem` element is not accessible. This is because when we render the `Async` component, the initial state is an empty array, on the initial render, the `useEffect` hook does not run, which results in `posts` state to be an empty array. After the component is mounted  the `useEffect` hook runs, and then the data is fetched from the `URL`, which takes a few milliseconds, and then finally the `posts` state is updated.

But when we run our tests, we are not waiting for the request `"Promise"` to be completed, due to which we see that the `listitem` element is an empty array with length `0`. We immediately get all the items on the screen with `getAllByRole()` function.

## Solution

We can overcome this problem of the `getAllByRole()` function fetching the values immediately by using a `"find"` selector from the screen property. The difference here is that all the `"find"` selector functions return a `Promise`, which needs to be fulfilled, before  the element can be selected from the `screen`. This is not the case with the `"get"` and `"query"` selectors, where they return `errors` or `null`/ `not null` respectively.

### Using `findAllByRole()`

For the `Async` component test suite, we will use the `findAllByRole()` method to find all the elements with the role of `listitem`. This will now wait for the HTTPS `fetch()` request to finish. It has a default timer of 1 second, that will return an error if the `fetch()` doesn't return the data on time. We can change this default timer in the third parameter of the `findAllByRole()` method.

Since this method returns a `Promise`, we need to `await` it so that it can complete. For this to work, our `test()` anonymous function, i.e the second argument in the `test()` function, can have the `async` keyword, to wait for the Promise from the `findAllByRole()` to complete.

{% code title="Async.test.js" %}
```jsx
//...
//...
describe(
  test('...', async() => {
    //arrange
    //act
    //assert
    const posts = await screen.findAllByRole('listitem');
    expect(posts).not.toHaveLength(0);
  })
);
```
{% endcode %}

### Results

With the above changes, our `Async Component Test Suite` should also pass without any errors.

![Async test suite pass results.](<../../.gitbook/assets/Screenshot 2021-05-12 at 22.47.17.png>)

## Next Steps

This solution here is okay, but still not ideal. For a better approach and solution to testing async code, refer to [Mocks](https://sydrawat.gitbook.io/react/miscellaneous/testing/mocks).
