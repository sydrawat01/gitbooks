---
description: Writing tests and grouping tests together in test suites.
---

# 🧪 Test & Test Suites

## First Custom Test

Typically, we write the tests in a file, which is placed / located as close as possible to the component that we are testing. Suppose we have a component called `Greeting`:

{% code title="Greeting.js" %}
```jsx
import React from 'react';

const Greeting = () => {
  return (
    <>
      <h2>Hello Human</h2>
      <p>Greetings from outer space!</p>
    </>
  )
}

export default Greeting;
```
{% endcode %}

To test this component, we will create a new file called `Greeting.test.js`, which will be located on the same level as `Greeting.js` file. Before we dive in to what we should write into the test file, here are some basic rules to follow when defining tests, or the `3 A's` :

![Three A's for writing a test.](<../../.gitbook/assets/Screenshot 2021-05-12 at 16.30.02.png>)

So now we're ready to write our own test for the `Greeting` component!

{% code title="Greeting.test.js" %}
```jsx
import {render, screen} from '@testing-library/react';
import Greeting from './Greeting';

test('renders hello human as text', () => {
  //Arrange
  render(<Greeting />);
  //Act ...nothing as of now
  //Assert
  const helloElement = screen.getByText('Hello Human');
  expect(helloEelement).toBeInTheDocument();
});
```
{% endcode %}

### Test Code Structure

A few things to not about the above example:

* `test` is a globally available function that is available throughout our React app.
* The `screen` property is somewhat similar to the virtual DOM, which has access to the elements to the DOM. The screen property has many different selectors, but mostly consists of various functions:
  * **get** : will throw an error if an element is not found.
  * **find** : return a promise.
  * **query** : won't return an error if element is not found.
* The `screen.getByText()` function takes in the text which needs to be matched, and also takes an optional second argument, which says if we want an `exact` match or not (`false` or `true`).

```jsx
const helloElement = screen.getByText('Hello Human', {exact: false});
```

* `expect()` function is also globally available in our app, which takes in the element that we have extracted from the `screen()` function, and then has a variety of function to assert the extracted element. We can also check for opposites here by using the `.not` property prefix to the available functions of `exact()` :

```jsx
expect(helloElement).not.toBeInTheDocument()
```

### Running the Test

After writing the test code for the `Greeting` component, it it time we run the tests.

```bash
yarn test
```

![Test results.](<../../.gitbook/assets/Screenshot 2021-05-12 at 16.55.54.png>)

## Test Suites

Multiple tests together as a bundle / group, are called test suites. Tests related to a feature or a component are usually bundled together in such testing suites. We can create such a testing suite by using the global function called `describe()`

The `describe` function also takes in two parameters:

* First, the description, about the category to which the grouped tests belong to.
* Second, an anonymous function, but we do not write the tests inside this function, but we put different tests in there.

{% code title="Greeting.test.js" %}
```jsx
//...
//...
describe('Greeting Suite', () => {
  test('renders hello human as text', () => {
    //Arrange...
    //Act...
    //Assert...
  }
})
```
{% endcode %}

Here's what the result looks like when a test suite is run (we can see the test suite name and within it, the one test that we had written within that test suite):

![Test Suite results.](<../../.gitbook/assets/Screenshot 2021-05-12 at 17.08.19.png>)
