---
description: Problems with "findAllByRole()" and overcoming them with Mocks.
---

# 🧪 Mocks

## Underlying Problems

In the previous section, we solved the problem of async code in our test suite using the `findAllByRole()` function, which helps us `aysnc` and `await` for a promise when the data is fetched from the server, i.e, the `fetch()` completes it's `Promise`.

But this approach has a few problems:

* Firstly, if we have multiple tests which fetch data from the server, it will cause an overload to our servers, since we will be hitting the fetch URL multiple times, sometimes leading to excess traffic in the network.
* Secondly, if we have a component that sends request to a server, i.e, the `POST` request, our test cases might start entering data into the database, or change the data in the database, which is actually not correct.

There are two approaches to solve these problems:

* Send a request to a fake server
* Don't send a real request (Mocks)

## Mocks

We do not test if the `fetch()` function runs correctly, but rather we check if the component behaves in the expected way depending on the outcomes of sending the requests.

So we check if the component behaves correctly when we get the response data, and if the component behaves correctly if we get an error. We DO NOT CHECK  whether technically sending the requests succeeds.

### Using Mocks

We typically replace the fetch function call with a mock function that "mocks" a fetch request, but isn't actually sending or receiving data to / from an actual server. This is a similar case if we are working with `localStorage`, where we do not want to trigger changes to the actual storage.

These are such common scenarios, that `Jest`, the testing tool that we are using under the hood, has built in support for mocking such functions. To change the `fetch()` browser default function to a mock function, we need to call `jest.fn()` on `window.fetch`, which creates a mock function, and will then replace the browser's `fetch()` functionality.

To learn more about the `jest.fn().mockResolvedValueOnce()`, refer to the [official Jest docs](https://jestjs.io/docs/mock-function-api#mockfnmockresolvedvalueoncevalue). What the `mockResolvedValueOnce` function does is, it allows us to set a value the fetch() browser default function should resolve to when it's being called. The resolved value from the `fetch()` request, resolves to an object. This object is then automatically resolved by the promise, which will automatically be resolved once we call the `mockResolvedValueOnce`. The `json()` function call in the actual `fetch()` call is an async function that returns a promise, so in our mock function as well, this property will be an async function, which in the end, results in an array of "_posts_", but here we will add a dummy post into this resultant array.&#x20;

{% code title="Async.test.js" %}
```jsx
import {render, screen} from '@testing-library/react';
import Async from './Async';

describe('Async Test Suite', async () => {
  test('Renders posts if request succeeds', () => {
    //arrange
    // 1. Mock fetch request
    window.fetch = jest.fn();
    window.fetch.mockResolvedValueOnce({
      json: async () => [{id: 'p1', title: 'First Dummy Post'}],
    });
    // 2. Render the component with the dummy data from the mock
    render(<Async />);
    //act...nothing
    //assert
    const posts = await screen.findAllByRole('listitem');
    expect(posts).not.toHaveLength(0);
  });
})
```
{% endcode %}
