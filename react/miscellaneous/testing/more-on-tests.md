---
description: Testing user interactions and state.
---

# 🧪 More on Tests

## User Interaction & State Changes

Suppose we have a button in our component, that changes the text that is rendered in the component. We want to test whether this button click is working or not, and check if the text is changed on the button click. This is where we "`ACT`" within our tests (from the `3 A's` of writing a test).

### Button click & State change

#### Greeting Component

In the `Greeting` component, we have a state that stores `true` / `false`, based on whether the `button` was clicked, and based on the state value, we render different `<p>` text to the component. We will be testing these use cases here.

{% code title="Greeting.js" %}
```jsx
import React, {useState} from 'react';

const Greeting = () => {
  const [changeText, setChangeText] = useState(false);
  
  const changeTextHandler = () => {
    setChangeText(true);
  }
  return (
    <>
      <h1>Hello Human</h1>
      {!changeText && <p>Greeting from outer space!</p>}
      {changeText && <p>Changed!</p>}
      <button onClick={changeTextHandler}>Change Text</button>
    </>
  );
}

export default Greeting;
```
{% endcode %}

### Test Button Click

Lets now create the test suite for this component, where we check for the text change upon the button click. For this, we would need the `userEvent` component from `@testing-library/user-event`, to trigger user events in the virtual `screen`. With this, we can perform all the events that the user can trigger, like click, double click, hovering, typing into inputs, etc. The `userEvent.click()` accepts the `screen` element as a parameter. We can select the button element by text, or even better, we can use the `getByRole()` method to get the `button` element from the `screen`. Since here we have only one button element, it is okay to select this element using the `getByRole()` function.

Here, in the test for button click, we need to "act", to simulate a button click event. After the button click simulation is done, we need to "assert" again, to check if the element \[text] has changed to "Changed!", and if  it does, the test will pass.

#### Greeting Test Code

{% code title="Greeting.test.js" %}
```jsx
import {render, screen} from '@testing-library/react';
import useerEvent from '@testing-library/user-event';

import Greeting from './Greeting';

describe('Greeting Component Test Suite', () => {
  test('Renders "Hello Human" as text', () => {
    render(<Greeting />); //arrange
    //act...nothing
    const helloElement = screen.getByText(/Hello Human/i);
    expect(helloElement).toBeInTheDocument(); //assert
  });
  test('Renders "Greeting from outer space!" if button is NOT clicked', () => {
    render(<Greeting />); //arrange
    //act...nothing
    const greetElement = screen.getByText('Greeting from outer space', {
      exact: false,
    });
    expect(greetElement).toBeInTheDocument(); //assert
  });
  test('Renders "Changed!" when the button IS CLICKED', () => {
    render(<Greeting />); //arrange
    // === ACT ===
    const buttonElement = screen.getByRole('button');
    userEvent.click(buttonElement);
    //assert
    const changedTextElement = screen.getByText('changed', {exact: false});
    expect(changedTextElement).toBeInTheDocument();
  });
});

```
{% endcode %}

### Edge Case

We need to also check if the "_Greeting from outer space_" message is hidden, when the button is clicked. This is especially handy when we forget to conditionally render something, and a conditional element is always show in our component.

Here, if we try to get the element by text after the button is clicked, it will throw an error, saying that it cannot find the element with the specified text. But this is specifically what we want, and since the `getByText()` will always throw and error in such a case, we need to use the `queryByText()` method  to get the element with the specified text. If the element with that text is not found, it returns a `null`, instead of an error. So finally we can assert that we `expect()`  the element that we got by `queryByText()`, `toBeNull()`.

{% code title="Greeting.test.js" %}
```jsx
import {render, screen} from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import Greeting from './Greeting';

describe('Greeting Test Suite', () => {
  //test 1
  //test 2
  //test 3
  test('Check if text "Greeting from outer space" is not visible',() => {
    render(<Greeting />);
    const buttonElement = screen.getByRole('button');
    userEvent.click(buttonElement);
    const hiddenElement = screen.queryByText('greeting from outer space', {
      exact: false,
    });
    expect(hiddenElement).toBeNull();
  });
})
```
{% endcode %}

### Results

Upon running the tests, we get the following output, where all the 3 tests in the test suite `Greeting Suite`, have passed:

![Greeting Test Suite results.](<../../.gitbook/assets/Screenshot 2021-05-12 at 21.17.36.png>)

## Testing Connected Components

Consider that the `Greeting` component also imports another component called `Output`, which in the end renders a paragraph with some text. Now we want to run the tests on the Greeting component, but it also has a new component, `Output`, which is connected to it. The good things about the render method in the `@testing-library/react` is that, it renders the entire component tree, so in a way this can be called an `Integration Test`, though the connected component does not have much logic of it's own.&#x20;

Of course, if the logic in the connected component becomes very large to handle on its own, like having state changes, etc., in that case it would need to have a separate test file of its own.

### Connected Components

{% code title="Output.js" %}
```jsx
import React from 'react';

const Output = ({children}) => {
  return <p>{children}</p>;
}

export default Output;
```
{% endcode %}

{% code title="Greeting.js" %}
```jsx
import Output from './Output';
//...
//...
const Greeting = () => {
  //...
  //...
  return (
    //...
    //...
    {!changeText && <Output>Greeting from outer space!</Output>}
    {changeText && <Output>Changed!</Output>}
  );
}

export default Greeting;
```
{% endcode %}

With the above changes, and no changes to the `Greting.test.js` file, our test cases will  execute and pass without any errors!

### Results

![Testing Connected Components.](<../../.gitbook/assets/Screenshot 2021-05-12 at 21.38.47.png>)
