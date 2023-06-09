---
description: >-
  Adding animations to list items in React using the TransitionGroup component
  from react-transition-group library.
---

# 📽 List Animation

## [TransitionGroup](http://reactcommunity.org/react-transition-group/transition-group)

The `<TransitionGroup>` component manages a set of transition components (`<Transition>` and `<CSSTransition>`) in a list. Like with the transition components, `<TransitionGroup>` is a state machine for managing the mounting and unmounting of components over time.

Note that `<TransitionGroup>` does not define any animation behavior! Exactly _how_ a list item animates is up to the individual transition component. This means you can mix and match animations across different list items.

## [Props](http://reactcommunity.org/react-transition-group/transition-group#TransitionGroup-props)

### [component](http://reactcommunity.org/react-transition-group/transition-group#TransitionGroup-prop-component)

`<TransitionGroup>` renders a `<div>` by default. You can change this behavior by providing a `component` prop. If you use React v16+ and would like to avoid a wrapping `<div>` element you can pass in `component={null}`. This is useful if the wrapping div borks your css styles.

**type**: `any`

**default**: `'div'`

### [enter](http://reactcommunity.org/react-transition-group/transition-group#TransitionGroup-prop-enter)

A convenience prop that enables or disables enter animations for all children. Note that specifying this will override any defaults set on individual children Transitions.

**type**: `boolean`

### [exit](http://reactcommunity.org/react-transition-group/transition-group#TransitionGroup-prop-exit)

A convenience prop that enables or disables exit animations for all children. Note that specifying this will override any defaults set on individual children Transitions.

**type**: `boolean`

## Example

Let's make a list of numbers which we can add and remove based on click handlers.

{% tabs %}
{% tab title="List Component" %}
{% code title="components/List.js" %}
```jsx
import {useState} from 'react';

import classes from "./List.module.css";

const List = () => {
  const [data, setData] = useState({
    items: [1, 2, 3]
  });

  const addItemHandler = () => {
    setData((prevState) => {
      let value = 1;
      if (prevState.items.length > 0) {
        value = prevState.items[prevState.items.length - 1] + 1;
      }
      return {
        items: prevState.items.concat(value)
      };
    });
  };
  const removeItemHandler = (idx) => {
    setData((prevState) => {
      return {
        items: prevState.items.filter((item, index) => index !== idx)
      };
    });
  };

  let listContent;
  if (data.items.length === 0) {
    listContent = <p>Please add items!</p>;
  } else {
    listContent = data.items.map((item, index) => (
      <li
        key={index}
        className={classes.listitem}
        onClick={removeItemHandler.bind(null, index)}
      >
        {item}
      </li>
    ));
  }

  return (
    <div className={classes.container}>
      <button className="button" onClick={addItemHandler}>
        Add Item
      </button>
      <p>Click to remove the item.</p>
      <ul className={classes.list}>{listContent}</ul>
    </div>
  );
};

export default List;
```
{% endcode %}
{% endtab %}

{% tab title="List Styles" %}
```css
.container {
  margin: 5px auto;
}

.list {
  list-style: none;
  text-align: center;
  margin: 0 auto;
  padding: 0;
  width: 280px;
}

.listitem {
  box-sizing: border-box;
  border: 1px solid green;
  padding: 10px;
  width: 100%;
  margin: 0 auto;
  border-radius: 5px;
  margin-bottom: 0.5rem;
}

.listitem:hover,
.listitem:active {
  cursor: pointer;
  background-color: #ccc;
}

```
{% endtab %}
{% endtabs %}

Since the `<TransitionGroup>` component does not define any animation behavior, we will use the `<CSSTransition>` component to define the animations in our list items!

The important thing here is,  we are not going to use the `in` property to control the state of our transition. The special thing about `<TransitionGroup>` is that it is able to handle multiple items, and is able to determine whenever one element changes, i.e, if it is removed or added. It will then manually set the `in` property on the wrapped `<Transition>` or `<CSSTransition>`component. We cannot control the `in` property for a dynamic list, hence `<TransitionGroup>` does that for us out of the box.

### Code snippet

{% tabs %}
{% tab title="List Component" %}
{% code title="components/List.js" %}
```jsx
//...
import TransitionGroup from 'react-transition-group/TransitionGroup';
import CSSTransition from 'react-transition-group/CSSTransition';

const List = () => {
  //...
  const listItem = listArr.items.map((item, index) => (
    <CSSTransition
      key={index}
      timeout={200}
      classNames={{
        enter: classes['fade-enter'],
        enterActive: classes['fade-enter-active'],
        exit: classes['fade-exit'],
        exitActive: classes['fade-exit-active'],
      }}
    >
      <li
        className={classes.ListItem}
        onClick={removeItemHandler.bind(null, index)}
      >
        {item}
      </li>
    </CSSTransition>
  ));
  
  return (
    <div>
      <button className="button" onClick={addItemHandler}>
        Add Item
      </button>
      <p>Click to remove the item.</p>
      <TransitionGroup component="ul" className={classes.List}>
        {listItems}
      </TransitionGroup>
    </div>
  );
}

export default List;
```
{% endcode %}
{% endtab %}

{% tab title="List Styles" %}
{% code title="components/List.module.ss" %}
```css
/*
* ...
*/

.fade-enter {
  opacity: 0;
}

.fade-enter-active {
  opacity: 1;
  transition: opacity 0.3s ease-out;
}

.fade-exit {
  opacity: 1;
}
.fade-exit-active {
  opacity: 0;
  transition: opacity 0.3s ease-out;
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

The final code example can be found [here at codesandbox.io](https://codesandbox.io/s/react-transition-group-x4coq?file=/src/App.js).

## Alternatives

There are a few popular alternatives to `react-transition-group` for animating a React app:

* [react-motion](https://github.com/chenglou/react-motion): for emulating real-world physics \[not actively maintained]
* [react-move](https://react-move.js.org/#/): for complex animations
* [react-router-transition](https://github.com/maisano/react-router-transition): for animating routing and routes on switch, but has limitations.

To use `react-transition-group` for route animations, refer to [this example](http://reactcommunity.org/react-transition-group/with-react-router/).

All of them are open-sourced, and have their own docs, if you would like to explore them.
