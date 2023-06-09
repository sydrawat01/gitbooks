---
description: Basic building blocks of a React app.
---

# 🧱 Components

## Components

Components are the **core building block of React apps**. Actually, React really is just a library for creating components in its core.

A typical React app therefore could be depicted as a **component tree** - having one root component ("App") and then an potentially infinite amount of nested child components.

Each component needs to return/ render some **`JSX`** code - it defines which HTML code React should render to the real DOM in the end.

**`JSX` is NOT HTML** but it looks a lot like it. Differences can be seen when looking closely though (for example `className` in `JSX` vs class in "normal HTML"). `JSX` is just syntactic sugar for JavaScript, allowing you to write HTML-ish code instead of nested `React.createElement(...)` calls.

When creating components, you have the choice between two different ways:

1. **Functional Components**

```jsx
const Cmp = () => {
  return <div> Some JSX </div>;
}
```

1. **Class-based Components**

```jsx
class Cmp extends Component {
  render() {
    return <div> Some JSX </div>
  }
}
```

It's best practice to use _functional components_ as much as possible.

### The create-react-app template structure

* **node\_modules**: contains all the project dependencies
* **public**: contains all the compiled and bundled project files
* **\[.gitignore, package.json, README.md, yarn.lock]**: listing out dependencies, project build, dev/env. Lock file for version control management. Ignore file is to exclude things from remote repo.
* **src**: contains all the styles and components

{% code title="App.js" %}
```jsx
import React, {Component} from "react"
import "./styles.css"

const App = () => {
  return (
    <div className="App">
      <h1>Hello React</h1>
      <h2>This is a react web page!</h2>
    </div>
  );
}

export default App;
```
{% endcode %}

{% code title="Index.js" %}
```jsx
import ReactDOM from "react-dom"
import App from "./app"

const rootElement = document.getElementById("root")
ReactDOM.render(<App />, rootElement);
```
{% endcode %}

In the `index.js` file, the `root` element is defined in the `index.html`, which is present in the public folder. The above `ReactDOM.render()` function call will bind the `<App />` component (defined in the `app.js` file) to the `rootElement`.

**NOTE:** The `render()` function call can have only 1 main `<div>` element, and we have to nest everything inside of this one main `<div>` element. This is somewhat loosened in _React v.16^_

But it is best to practice to have only 1 root element or component.

### Understanding `JSX`

Consider the example in `app.js` file:

{% code title="App.js" %}
```jsx
import React, {Component} from 'react'
import './style.css'

const App = () => {
  return (
    <div className="App">
      <h1>Hello React</h1>
      <h2>This is a React App</h2>
    </div>
  );
}

export default App;
```
{% endcode %}

If you notice the `<div>` component, the `className` is not the usual HTML syntax, it's JSX. So, at the back, this is not exactly how it is compiled.

The `<div className="App">` `JSX` code is converted to:

```jsx
return React.createElement(div, null, 'h1', 'Hello React');

// h1 Hello React
```

In the `React.createElement()` function, the parameter list is:

* **div:** This is the first parameter, which translates to which element (_or a React component)_ should be rendered in the DOM.
* **null:** This is the second parameter, which is the config for the JS object.
* **h1, 'Hello React':** The parameters after the first two are referred to as `children`. These children components or elements are the ones to be nested inside the `div` in the first parameter (_which could also be a React component_). There can be infinite number of children.

But in the above example, the `h1` tag is not rendered as an HTML `h1` tag, it's rendered as plain text. So, as mentioned above, we have to nest this element inside the main `div` element, like so:

```jsx
return React.createElement(div, {className: 'App'}), React.createElement(h1, null, 'Hello React'));

// <h1>Hello React</h1>
```

So here, instead of `null` in the second parameter, we pass the `JSX` element property of `className` , and then nest the `h1` element within the `div`.

This is what `JSX` get compiled to by the build tools included in the create-react-app. Hence, this is why we need to import `React, {Component} from 'react'` even though we never use it in our `JSX` code.

### React Components

**Functional Components**

```jsx
import React, {Component} from 'react'
const Comp = () => {
  return(
    <div>Some JSX</div>
  );
}
```

**Class Components**

```jsx
import React, {Component} from 'react'
class Comp extends Component {
  render() {
    return(
      <div>Some JSX</div>
    )
  }
}
```

### Dynamic content in components

#### `props`

Attribute values that we pass to a component to render dynamic data.

Example:

```jsx
<Person name='Sid' age='25' />
<Person name='Max' age='26' />

// In the component
const Person = ({name, age}) => {
  return (
    <p> I am {name} and {age} years old</p>
  );
}
```

In the above example, I have used _object de-structuring_ to make the code more "readable". If you're not familiar with object de-structuring, you can use the other syntax as well:

```jsx
const Person = (props) => {
  return(
    <p>I am {props.name} and {props.age} years old</p>
  );
}
```

This, obviously is tedious to write since we have to use `props.<propertyName>` every time we need to access the property of that component. So, it's always better to use object de-structuring whenever possible.

#### The `children` prop

If there is any data between the opening and closing tags of a React component, we call those data types as children props.

Example:

```jsx
<Person name="Sid" age="25">Hobbies: Art</Person>
```

In our React component:

```jsx
const Person = ({name, age, children}) => {
  return (
    <div>
      <p>I am {name} and {age} years old</p>
      <p>{children}</p>
    </div>
  );
}
export default Person
```

`'children'` is a reserved keyword in React, so if there are no child components within the parent component, i.e, between the opening and closing tags of the outer component, then `children` will be `null`.

#### `state`

The state of a component is managed from within the component.

**NOTE:** These are available only in class based components, which extend the `Component` class from `react`.

`State` is a reserved keyword and is of type `Object`.

Example:

```jsx
state = {
  persons: [
    {name: 'Sid', age:'26'}
    {name: 'Max', age:'27'}
    {name: 'Dennis', age:'28'}
  ]
}
```

To access the state properties on the components:

```jsx
<Person name={this.state.persons[0].name} age={this.state.persons[0].age} />
```

### Handling events with methods

Refer to [Event Handling page](https://app.gitbook.com/s/-MZvh-eMIQtgB00n2d-q/introduction/03%20-%20Event%20Handling%2076db7ecaeaa9441f916669dcd14966a9.md) on this notebook for more events that we can listen to.

Example:

```jsx
switchNameHandler = () => {
  console.log('clicked')
}

<button onClick={this.switchNameHandler}>Switch Name</button>
```

### Manipulating the state

#### **Class based components**

```jsx
state = {
  persons: [
    {name: 'Sid', age:'26'}
    {name: 'Max', age:'27'}
    {name: 'Dennis', age:'28'}
  ],
  otherState: 'some other state value'
}

switchNameHandler = () => {
  this.setState({
    persons: [
      {name: 'Sid', age:'27'}
      {name: 'Max', age:'27'}
      {name: 'Dennis', age:'29'}
    ]
  });
}
```

The `setState()` function is provided by React `Component`, which we import initially.

In the `setState()`, we only change a part of the original `state`, then the updates to the original `state` are merged from the `eventHandler`, w

#### **Functional Components**

`React Hooks` are the way in which we manipulate state in functional components in React.

There are many hooks, but we will use the `useState` hook to manipulate state in the functional components here.

**NOTE:** We can have as many `useState` hooks in our React functional component.

The `useState` hook returns an array with exactly 2 elements:

* 1st element is the **current state**
* 2nd element is a **function that allows us to update the state**

```jsx
import React, {useState} from 'react'
```

**NOTE:** We can use array de-structuring here to capture both the elements that the `useState` hook provides us with.

```jsx
const [personState, setPersonState] = useState({
  persons: [
    {name: 'Sid', age:'26'}
    {name: 'Max', age:'27'}
    {name: 'Dennis', age:'28'}
  ],
  otherState: 'some other state value'
})
```

Let's implement the `eventHandler`

```jsx
const switchNameHandler = () => {
  setPersonState({
    persons: [
      {name: 'Sid', age:'27'}
      {name: 'Max', age:'27'}
      {name: 'Dennis', age:'29'}
    ]
  });
}
```

In hooks, we do not use the `this` keyword to call the event handler

```jsx
<button onClick={switchNameHandler}>Switch Name</button>

//add props to Component

<Person name={personState.persons[0].name} age={personState.persons[0].age}/>
```

**NOTE:** When we are updating the state using the `useState()` hook, we need to manually update or add the other data as well, because this hook replaces the whole state, and does not merge the state like back in `this.setState()`

```javascript
console.log(personState) //before clicking 'Switch Name'
/*
    persons: [
    {name: 'Sid', age:'26'}
    {name: 'Max', age:'27'}
    {name: 'Dennis', age:'28'}
    ],
    otherState: 'some other state value'
*/
```

```javascript
console.log(personState) //after clicking 'Switch Name'
/*
    persons: [
    {name: 'Sid', age:'27'} --> age updated
    {name: 'Max', age:'27'}
    {name: 'Dennis', age:'29'} --> age updated
    ],
    ~~otherState: 'some other state value'~~ --> state property is lost
*/
```

To retain `otherState` within the state when using `useState` hook, we need to manually add the state property when manipulating the state:

```jsx
const switchNameHandler = () => {
  setPersonState({
    persons: [
      {name: 'Sid', age:'27'}
      {name: 'Max', age:'27'}
      {name: 'Dennis', age:'29'}
    ],
    otherState: 'some other state value'
  });
}
```

#### Passing method reference b/w components

We can also pass methods ( as a reference) as `props` to components!

```jsx
const switchNameHandler = () => {
  //perform state manipulation
}

<Person
  name={this.state.persons[0].name}
  age={this.state.persons[0].age}
  click={this.switchNameHandler}
/>
```

In the `Person` component:

```jsx
import React, {Component} from 'react'
const Person = ({name, age, click, children}) => {
  return (
    <p onClick={click}>
      Hi! I am {name} and {age} years old.
      {children}
    </p>
  );
}
```

In the above example, we passed a function or method reference to a `Person` component, which does not have direct access to the state, but still manipulates the state in the app.

#### Two way binding in components

Listen for text input and change the name of the person accordingly. Also show the initially set name in the text field.

{% code title="App.js" %}
```jsx
nameChangeHandler = (event) => {
    this.setState({
    persons: [
        {name: 'rahul', age:'28'},
        {name: event.target.value, age: 28},
        {name: 'dennis', age: 29}
    ]
    })
}

render() {
  return(
    <Person 
      name={this.state.persons[1].name}
      age={this.state.persons[1].age}
      change={this.nameChangeHandler}
    />
  );
}
```
{% endcode %}

{% code title="Person.js" %}
```jsx
const Person = ({name, age, change, children}) => {
  return (
    <div>
      <p>
        I am {name} and {age} years old
        {children}
      </p>
      <input type="text" onChange={change} value={name}/>
    </div>
  );
}
```
{% endcode %}

### Styling React Components

*   **Using `stylesheets`**: This is same as using the basic HTML, CSS and JS web page, where we have all our stylesheets in a separate file, and then we inject these files into the component.

    **NOTE:** The styles via external files are global.

We `import` the stylesheet like so:

```jsx
import './style.css'
```

`Webpack` is a tool that helps it recognize and compile it _behind-the-scene._

* **Inline styles**: For inline styles, we create a `style object` inside the component, inside the `render()` method call:

```jsx
render() {
  const style={
    backgroundColor: 'white',
    font: 'inherit',
    border: '1px solid black',
    padding: '8px',
    cursor: 'pointer'
  }
  return (
    <button style={style}> Click Me</button>
  );
}
```

**NOTE:** All the styles in inline styles are `camelCase`. Styles that are inline have a scope limited to that component itself!

## Stateless v/s Stateful Components

It does not mean if a component is a class-based component, it is a stateful component, and if a component is a functional component, it is a stateless component. But, this was the scenario before the introduction of React hooks in v16.

So, a stateful component is the one, which implements the `useState()` or `state` , and the one that doesn't, is a stateless component.

### Class based v/s Functional Components

#### **Class Based**

```jsx
class XY extends Component {...}
```

* Has access to state
* Uses Lifecycle Hooks

Access to `state` and `props` via `this` keyword.

```jsx
this.state.XY
this.state.props
```

**NOTE:** Unless you want to manage state or access to lifecycle hooks, do not use class based components!

#### **Functional Components**

```jsx
const XY = () => {...}
```

* Access to state using `useState()`
* No lifecycle hooks

Access to props via `props`

```jsx
props.XY
```

**NOTE:** This should be the preferred way to create components and state management, without the use of lifecycle hooks!
