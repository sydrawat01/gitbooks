---
description: Working with react-transition-group
---

# 📽 Animations in React

## Basics of Animation

In this section, we'll learn how to animate basic HTML elements \[or JSX elements] in React.

### Creating the components

Suppose we want to animate the showing and hiding of a modal. First, let's create the modal & backdrop components, and add the show & hide features to it. We'll be using CSS Modules here.

{% tabs %}
{% tab title="Modal" %}
{% code title="components/Modal.js" %}
```jsx
import classes from './Modal.module.css';

const Modal = (props) => {
  const cssClasses = `${classes.modal} ${
    props.show ? classes.modalOpen : classes.modalClosed
  }`;
  
  return (
    <div className={classes.modal}>
      <h1>Dummy Modal</h1>
      <button onClick={props.close}>Dismiss</button>
    </div>
  );
}

export default Modal;
```
{% endcode %}
{% endtab %}

{% tab title="Backdrop" %}
{% code title="components/Backdrop.js" %}
```jsx
import classes from './Backdrop.module.css';

const Backdrop = (props) => {
  const cssClasses  = `${classes.backdrop} ${
    props.show ? classes.backdropOpen : classes.backdropClosed
  }`;
  
  return <div className={cssClasses} onClick={props.close}/>
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Adding styles

{% tabs %}
{% tab title="Backdrop" %}
{% code title="components/Backdrop.module.css" %}
```css
.backdrop {
  position: fixed;
  z-index: 100;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0,0,0,0.65);
}

.backdropOpen {
  display: block;
}

.backdropClose {
  display: none;
}
```
{% endcode %}
{% endtab %}

{% tab title="Modal" %}
{% code title="components/Modal.module.css" %}
```css
.modal {
  postion: fixed;
  box-sizing: border-box;
  top: 25%;
  left: 35%;
  width: 50%;
  z-index: 200;
  padding: 10px;
  text-align: center;
  border-radius: 15px;
  background-color: white;
}

.modalOpen {
  animation: openModal 0.4s ease-out forwards;
}

.modalClose {
  animation: closeModal 0.4s ease-out forwards;
}

@keyframess openModal {
  0%{
    opacity: 0;
    transform: translateY(-100%);
  }
  50%{
    opacity: 1;
    transform: translateY(20%);
  }
  100%{
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes closeModal {
  0%{
    opacity: 1;
    transform: translateY(0);
  }
  50%{
    opacity: 0.8;
    transform: translateY(60%);
  }
  100%{
    opacity: 0;
    transform: translateY(-100%);
  }
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Using the components

{% code title="App.js" %}
```jsx
import {useState} from 'react';
import Backdrop from './components/Backdrop';
import Modal from './components/Modal';

const App = () => {
  const [showModal, setShowModal] = useState(false);
  
  const showModalHandler = () => {
    setShowModal(true);
  }
  const hideModalHandler = () => {
    setShowModal(false);
  }
  return (
    <>
      <h1>React Animations</h1>
      <Backdrop show={showModal} close={closeModalHandler} />
      <Modal show={showModal} close={closeModalHandler} />
      <button onClick={showModalHandler}>Show Modal</button>
    </>
  );
}
```
{% endcode %}

## Problems with basic animations

There are a few basic issues with the above approach:

* The `Backdrop` & `Modal` components are always rendered in the DOM, because we are not conditionally rendering them, and hence are always present, irrespective of whether we need them or not. We are showing and hiding the modal using CSS, not actually attaching and removing these components from the DOM, which should be the actual behavior.
* To conditionally render the Backdrop & Modal components, we can do that, but it will break the animation functionality. The problem here will arise when we close the modal. When we change the state of `showModal` to false, React will immediately remove the component from the DOM, and our animation `keyframes` will not be able to run on that component.

```jsx
const App = () => {
 //...
 //...
 return (
  {showModal && <Backdrop show={showModal} close={closeModalHandler} />}
  {showModal && <Modal show={showModal} close={closeModalhandler}/>}
 );
}
```

We overcome these issues using a third party library called [`react-transition-group`](http://reactcommunity.org/react-transition-group/).
