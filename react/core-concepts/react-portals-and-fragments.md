---
description: >-
  Fragments: Built-in, out of the box HOC in CRA. Portals: to move React
  components to some other place in the Real DOM.
---

# 🕳 React Portals & Fragments

## Rendering adjacent JSX elements

Till now, we have been wrapping our JSX elements within one wrapping `<div>` JSX DOM element. Though it is necessary to have just one wrapping element, it is not a compulsion to use `<div>` DOM element.

Hence, we create our own component (not a React component) to avoid using a `DOM element`.

{% tabs %}
{% tab title="Aux" %}
{% code title="Aux.js" %}
```jsx
const Aux = props => props.children;

export default Aux;
```
{% endcode %}
{% endtab %}

{% tab title="App" %}
{% code title="App.js" %}
```jsx
//...
render() {
  return(
    <Aux>
      <h1> Some text </h1>
      <p> This is subtext</p>
    </Aux>
  );
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

## `React.Fragment`

React provides the same functionality above, for wrapping adjacent JSX elements, out-of-the box. Under the hood, it works just as same as our `Aux` component above.

{% code title="App.js" %}
```jsx
import React, {Component, Fragment} from 'react';

class App extends Component {
  //...
  render() {
    return(
      <Fragment>
        <h1> Some text </h1>
        <p> This is subtext </p>
      </Fragment>
    );
  }
}
```
{% endcode %}

_Can also use the dot notation_:

```jsx
return(
  <React.Fragment>
    <h1>Some text</h1>
    <p>This is subtext</p>
  </React.Fragment>
);
```

Another way to use React.Fragment is \[depends on the work flow you have, it works with the `create-react-app` project setup]:

```jsx
return (
  <>
    <h1><Some Text</h1>
    <p>This is subtext</p>
  </>
);
```

## React Portals

Overlay content shouldn't be deeply nested within components. Take an example of a modal, if it is deeply nested within a component, it's bad practice, since the modal should be above all other things in the webpage! It could be working (even if it's nested deeply) because of they styles that we provide in the CSS, but it's bad practice to write such code.

It is similar to creating a `button`, but with a `div`, and a click `eventListener` on that div. Technically, the div-button will work, but that does not mean it's good code. It's difficult to understand, has accessibility issues, and is generally regarded as bad practice. Hence we should avoid this.

Look a the example below:

<div align="center">

<img src="../.gitbook/assets/Screenshot 2021-05-19 at 16.03.36.png" alt="DOM before using React.Portal">

</div>

This is where `MyModal` component is being used in our App. On the left hand side, we see our React code, and the modal does not seem to be deeply nested, but when this is rendered to the real DOM, it may turn out to be nested something similar to the right hand side, where the `div` with class `my-modal` is nested withing a `<section>`. This is not ideal code and is bad practice.

To overcome this problem, and remove the `MyModal` component from being deeply nested within the real DOM, we use something called React Portals, which would make our code somewhat as below, in the real DOM (right hand side), without requiring any changes on the React code side (left hand side):

![DOM after using React.Portal](<../.gitbook/assets/Screenshot 2021-05-19 at 16.07.28.png>)

### How do portals work?

Portals need two things:

* A place where you want to port the component to
* Need to let the component know that it should have a portal to that place

We can do this by adding `div` in the `/public/index.html` file, and giving the `div`appropriate `id` to use them in our App.

{% code title="public/index.html" %}
```markup
<!--...-->

<!--just above the root div, we add the divs for backdrop and modal -->
<div id="backdrop-root"></div>
<div id="overlay-root"></div>
<div id="root"></div>
```
{% endcode %}

Now, let's use portals to render our backdrop and modal overlay as close as possible to the root, i.e, on the `backdrop-root` and `overlay-root`.

In `Modal.js`:

{% code title="Modal.js" %}
```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import classes from './Modal.module.css';
import Card from '../Card/Card';
import Button from '../Button/Button';

const Backdrop = ({ onConfirm }) => {
  return <div className={classes.backdrop} onClick={onConfirm}></div>;
};
const ModalOverlay = ({ title, message, onConfirm }) => {
  return (
    <Card className={classes.modal}>
      <header className={classes.header}>
        <h2>{title}</h2>
      </header>
      <div className={classes.content}>
        <p>{message}</p>
      </div>
      <footer className={classes.actions}>
        <Button type="button" onClick={onConfirm}>
          Close
        </Button>
      </footer>
    </Card>
  );
};
const Modal = ({ title, message, onConfirm }) => {
  return (
    <>
      {ReactDOM.createPortal(
        <Backdrop onConfirm={onConfirm} />,
        document.getElementById('backdrop-root')
      )}
      {ReactDOM.createPortal(
        <ModalOverlay
          title={title}
          message={message}
          onConfirm={onConfirm}
        />,
        document.getElementById('overlay-root')
      )}
    </>
  );
};

export default Modal;
```
{% endcode %}

So, we can use `ReactDOM.createPortal()` to move our JSX code (in the end HTML code since it renders to the real DOM), to any place in the DOM. With this, we can move our component's HTML code anywhere in the DOM.

### Syntax

```jsx
import ReactDOM from 'react-dom';
//...
//...
return(
  <>
    {ReactDOM.createPortal(
      <ReactComponent someProp={prop.someProp}/>,
      document.getElementById('id-in-index-html')
    )}
  </>
);
```
