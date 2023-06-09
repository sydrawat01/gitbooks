---
description: A third party library to overcome basic animation limitations in React.
---

# 📽 React Transition Group

## Installation

```bash
yarn add react-transition-group
```

## Transition

The `Transition` component lets us describe a transition from one component state to another "_over time"_ with a simple declarative API. Most commonly it is used to animate the `mounting` and `unmounting` of a component, but it can be used to describe in-place transitions as well.

### Transition States

By default, the Transition component does not alter the behavior of the component it renders, it only tracks the "_ENTRY"_ & _"EXIT"_ state of the components. There are 4 main states that a Transition can be in:

* `entering`
* `entered`
* `exiting`
* `exited`

```jsx
import {useState} from 'react';
import Transition from 'react-transition-group/Transition';

const App = () => {
  const [inProp, setInProp] = useState(false);
  
  return (
    <div>
      <Transition in={inProp} timeout={500}>
        {state => <p>{state}</p>}
      </Transition>
      <button onClick={() => setInProp(true)}>Click to Enter</button>
    </div>
  );
}

export default App;
```

### [Transition Props](http://reactcommunity.org/react-transition-group/transition#Transition-props)

The `Transition` state is toggled via the `in` prop. When `true`(_on button click_), the component begins the **"enter"** stage. During this stage, the component is in the `entering` state for the duration of the transition, i.e, 500ms, as defined in the `timeout` prop. It then moves to the `entered` state once the duration is completed.

When the `in` prop is `false`, the same thing happens, except the state moves from `exiting` to `exited` in the **"exit"** stage.

The animation timing prop, `timeout`, can be configured according to our needs:

```jsx
// all times are in milliseconds
const animationTime = {
  appear: 500,
  enter: 500,
  exit: 1000,
}

//...
  <Transition in={inProp} timeout={animationTime}>
   //{state => ...}
 </Transition>
   
```

We can use these states, along with `Transition` props like `mountOnEnter`, `unmountOnExit` to add or remove the component from the DOM, respectively.

{% code title="Modal.js" %}
```jsx
import Transition from 'react-transition-group/Transition';
import classes from './Modal.module.css';

const Modal = (props) => {
  const cssClasses = `${classes.modal} ${
    props.show ? classes.modalOpen : classes.modalClosed
  }`;
  
  return (
    <Transition in={props.show} timeout={400} mountOnEnter unmountOnExit>
      {state => {
        const cssClasses = `${classes.modal} ${
          state === 'entering'
          ? classes.modalOpen
          : state === 'exiting'
          ? classes.modalClose
          : null
        }`;
        return (
          <div className={cssClasses}>
            <h1>Dummy Modal</h1>
            <button onClick={props.close}>Dismiss</button>
          </div>
        );
      }}
    </Transition>
  );
}

export default Modal;
```
{% endcode %}

### [Transition Events](http://reactcommunity.org/react-transition-group/transition#Transition-prop-onEnter)

Sometimes we want to execute some code when the state of the animation finishes, and not just change what renders on the screen. For this, we have various callbacks we can add to function props and execute in a `Transition` component, which will run on various states of the animation.

```jsx
<Transition
  in={inProp}
  timeout={animationTime}
  mountOnEnter
  unmountOnExit
  onEnter={() => console.log('on enter')}
  onEntering={() => console.log('on entering')}
  onEntered={() => console.log('on entered')}
  onExit={() => console.log('on exit')}
  onExiting={() => console.log('on exiting')}
  onExited={() => console.log('on exited')}
/>
```

All these Transition properties can be useful for staggered animations, where we would wait for one animation to finish before we can run another animation, and execute some code in between these animations.

## [CSSTransition](http://reactcommunity.org/react-transition-group/css-transition)

`CSSTransition` applies a pair of class names during the `appear`, `enter`, and `exit` states of the transition. The first class is applied and then a second `*-active` class in order to activate the CSS transition. After the transition, matching `*-done` class names are applied to persist the transition state.

### [CSSTransition classNames](http://reactcommunity.org/react-transition-group/css-transition#CSSTransition-prop-classNames)

So instead of manually adding CSS classes to our component when the Transition is `entering` or `exiting`,  we can make use of `CSSTransition` component, with the `classNames` property. This property takes in an object, if you want to give alias to your classes or if you are using CSS Modules. If you're using global CSS styles, it just takes in the base class name, and attaches the postfix classes by itself. The object keys are the keywords in the `react-transition-group` package, and should be the same.

```jsx
// Using alias or CSS Modules
classNames = {{
  appear: 'my-appear',
  appearActive: 'my-appear-active',
  appearDone: 'my-appear-done',
  enter: 'my-enter',
  enterActive: 'my-enter-active',
  enterDone: 'my-enter-done'
  exit: classes.exit
  exitActive: classes['exit-active'],
  exitDone: classes['exit-done'],
}}

// Global CSS Styles
className = "fade-slide";
/* This will then be automatically translated to 
 * "fade-slide-enter-active" class when the component
 * is in 'entering' state. We would still need to define this class
 * in the CSS properties for that component.
 */
```

### Using `CSSTransition`

{% code title="components/Modal.js" %}
```jsx
import CSSTransition from 'react-transition-group/CSSTransition';

import classes from './Modal.module.css';

const animationTime = {
  enter: 400,
  exit: 1000,
}

const cssClasses = {
  enterActive: classes.modalOpen,
  exitActive: classes.modalClose,
}

const Modal = (props) => {
  return (
    <CSSTransition
      in={props.show}
      timeout={animationTime}
      mountonEnter
      unmountOnExit
      classNames = {{...cssClasses}}
    >
      <div className={classes.modal}>
        <h1>Dummy Modal</h1>
        <button onClick={props.close}>Dismiss</button>
      </div>    
    </CSSTransition>
  );
}

export default Modal;
```
{% endcode %}
