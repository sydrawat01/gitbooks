---
description: Required tooling and setup required for testing a React app.
---

# 🧫 Setup

## Required tools & Setup

We need some basic tools to setup testing in our React app. The below mentioned tools are already setup for us when we create a new React app using `create-react-app`

Both the below libraries come preinstalled with the `create-react-app` command.

### Jest

This is a tool that helps us run our tests and asserting the results.\
[Read more about Jest](https://jestjs.io/docs/getting-started)

### React Testing Library

This is a tool for "simulating" (rendering) our React app / components.\
[Read more about React Testing Library](https://testing-library.com/docs/react-testing-library/intro/)\
[Testing custom hooks with React Testing Library](https://github.com/testing-library/react-hooks-testing-library)

## &#x20;Example Test

Create a new React app using the `create-react-app` command:

```bash
npx create-react-app testing
```

In the `src/` folder, we will have the following files:

* App.js
* App.test.js
* index.js
* setupTests.js

### Understanding the code

All of the above files are already created with some content in them. The interesting file to us here is the `App.js` file. It contains the actual code required to test our component in `App.js` file. The `setupTests.js` file contains the imports and basic setup required for running tests on our React app / components.

{% code title="App.test.js" %}
```jsx
import {render, screen} from '@testing-library/react';
import App from './App';

test('renders learn a react link', () => {
  render(<App />);
  const linkElement = screen.getByText('learn react'/i);
  expect(linkElement).toBeInTheDocument();
});
```
{% endcode %}

The structure of the above file is important. We have a `test()` function that accepts two arguments:

* The first, a description of the test. Particularly useful if we have multiple tests, helps distinguish tests.
* Second, an anonymous function which contains the actual test code, which executes when we run our test.

In the above example, inside the `test` function, we `render` the `App` component, look for an element which has a text "learn react" (case insensitive), and then checks if such an element exists in the DOM.

### Running the example test

We use the per-installed `test script` that comes bundled with `create-react-app`. To run this script:

```bash
#yarn
yarn test

#npm
npm test
```

Upon running the above command in the terminal, inside the project folder, we get an output similar to the one shown below:

![Running the tests.](<../../.gitbook/assets/Screenshot 2021-05-12 at 15.53.40.png>)

This terminal prompt asks us a variety of options on how we want to run our test, and then executes all the files that end with `.test.js`

We run all the tests here, so we enter `'a'` as the option to the terminal prompt. This runs our test defined in the `App.test.js` file. Upon successful test completion, we get a prompt like this:

![Test results.](<../../.gitbook/assets/Screenshot 2021-05-12 at 15.56.46.png>)

Here, in the results, we can see the test description, which we passed as the first parameter to the `test()` function in our `App.test.js` file. We can see all the test suites and the tests that were run, and those of which that have passed.

### Watch for changes

In case we change the `App` component to render something other than 'Learn React' to something like 'Learn Angular', the tests would fail. The test results showing that the test failed is done automatically since the testing library watched all our files for changes, and runs the tests if anything does change, and gives us the output on the terminal.

![A failed test result.](<../../.gitbook/assets/Screenshot 2021-05-12 at 16.02.12.png>)

In the above screenshot, we can see that the test with the description '_renders learn react link'_ has failed. We get a description of why that test has failed:  "_**TestingLibraryElementError: Unable to find an element with the text: /learn react/i. This could be because the text is broken up by multiple elements. In this case, you can provide a function for your text matcher to make your matcher more flexible.**_".&#x20;

It also shows us the component where the test has failed, i.e, the `<App />` component here, and also shows what part of the `test()` function has failed by pointing it out using  the arrow-head (> / ^) symbols.

The result summary again shows how many test suites have failed, and how many tests have failed overall.
