---
description: >-
  Increasing performance using useCallback() and React.memo() and useMemo()
  hooks.
---

# 🛠 Performance Optimization

## React

In this section, we will learn how React works internally, and why it's fast.

![How React works behind the scenes.](../.gitbook/assets/Screenshot\_2021-05-01\_at\_23.30.12.png)

### React vs React-DOM

* React determines how the component tree looks like currently and how it **should** look. \[`virtual DOM`]
* React-DOM the receives the differences (the required changes), which then manipulates the real DOM.

When the `state`, `props` or the `context` of a component changes, the component is re-evaluated by React. But re-evaluating the component IS NOT THE SAME as re-evaluating or re-rendering the DOM.

![React-DOM and the Real DOM.](../.gitbook/assets/Screenshot\_2021-05-01\_at\_23.38.25.png)

### Virtual DOM diffing

* The functional component where we manage the `state`, `context` or `props` is ALWAYS re-evaluated whenever there is a state change/ prop change (based on the state).
* If a parent functional component is re-evaluated or re-executed, then all it's child components will also be re-executed and re-rendered!

![Virtual DOM diffing.](../.gitbook/assets/Screenshot\_2021-05-01\_at\_23.46.02.png)

The issue here is, when the parent component is re-evaluated, their child component is also re-evaluated, even though it might not be necessary to re-evaluate the child component! To overcome this problem, we have `React.memo()` method and `useCallback()` hook, which help us in nor re-evaluating child components or a child component branch altogether!

## `React.memo()`

Preventing unnecessary re-evaluations with `React.memo()`. We wrap the component export with the `React.memo()` function call, which we want to prevent from re-evaluating on prop/state changes that are not related to our component.

```jsx
const DemoOutput = () => {
  //...
  //...
  //...
}

export default React.memo(DemoOutput);
/*
* checks and compares the previous & current values of
* props, states, etc. If there are changes, only then it is
* re-evaluated, else re-evaluation is skipped!
*/
```

This is more useful on a tree of child components, rather than adding `React.memo()` to each individual child components. This way, we cut off the entire child component branch from the component tree and save it from re-evaluation.

* Components that have objects, arrays in their props, and functions that are defined inside the component function \[_basically, all non-primitive data types_] passed to a child component will be re-evaluated, since `React.memo()` cannot compare non-primitive data types.

```jsx
const App = () => {
  //...
  const onTogglehandler = () => {...}

  return(
    //...
    <Button onClick={onToggleHandler}>Click Here</Button>
  )
}
```

Here, when the `App` component re-evaluates, the `onToggleHandler` is re-created, since `React.memo()` cannot differentiate between or compare non-primitive data types. The `Button` component, i.e, the child component of the `App` component, also re-evaluates when the `App` component is re-evaluated.

To prevent function re-creation, we use the `useCallback` hook, and for non-primitive data types, like arrays and objects, we use `useMemo` hook, both of which, are provided by React.

## `useCallback` hook

This hook prevents the re-creation of a function when a component re-evaluates. This helps us store a function, and in turn, tells React that the specified function need not be re-created. By doing this, the function is stored at the same address, located by a function pointer to the address (when the function was created the first time the component was rendered to the Real-DOM). So the comparison in changes can be done easily.

### Syntax

```jsx
import {useCallback} from 'react';
//...
//...
const MyComponent = () => {
  //...
  //...
  const onToggleHandler = useCallback(()=> {
    //...
    //...
  }, [])
}
```

The `useCallback` hook takes in two parameters:

* First, an anonymous function, which is the function that we want which should be created only once, whenever the component is re-evaluated.
* Second, a dependencies array, which contains the list of dependencies on which the first parameter depends.

When using `useCallback` hook, we need to pass the dependencies array because we have a closed lexical scope, and thereby tell React to store the variables, along with the function defined in `useCallback` , in memory to not be re-created!

Consider the following example:

```jsx
import {useState, useCallback} from 'react';
//...
//...
const [allowToggle, setAllowToggle] = useState(false);
const [showPara, setShowPara] = useState(false);

const allowHandler = () => {
  setAllowToggle(true);
}

const toggleParaHandler = useCallback(() => {
  if(allowToggle) {
    setShowPara(prevState => !prevState)
  }
}, [allowToggle]);
```

Ideally, we should add all the variables we use inside of the anon function of `useCallback`. But here, since the `setShowPara` function is returned by the `useState` hook, and is managed internally by React, it is never bound to change, so we can avoid adding it to the dependencies array. `allowToggle` needs to be added to the dependencies array because it changes it's value, and based on it's value, the anon function execution is dependent. Hence we add it to the list.

If we do not provide the dependencies array, or leave it empty, the default value of `allowToggle`, which is `false`, is created and stored within the memory to be used later, and not re-created when the component re-evaluates.

When we add the dependencies array with the specified dependencies, we are using the latest values for the variables that are stored in the memory, that were _**only created once, but only keep updating their values**_.

The initialization of the `state` is not done again and again when the component re-evaluates. This is because `state` is managed by React, and is only initialized once, when the component attaches/renders to the Real DOM for the first time.

## State Scheduling & Batching

![State scheduling and batching.](../.gitbook/assets/Screenshot\_2021-05-02\_at\_00.38.40.png)

### Scheduling

Since we have multiple state changes scheduled, it is recommended that we use the anonymous function way to update the state. This ensures that the state changes are processed in-order and if we require the previous state, we get the latest state value for it.

### Batching

Suppose we have two or more different, but synchronous state update function calls, one after another, without any promises or timeout, etc. React `batches` all such requests to update the state into one single state update schedule, and when this scheduled state change takes place, the state sin the respective places are updated, and then the component is re-evaluated.

## `useMemo()`

We use this hook if we want to prevent recreation of non-primitive data types, except functions. Function recreation is handled by `useCallback` hook. The `useMemo` hook is used to prevent recreation of non-primitive data types like arrays and objects.

The `useMemo()` also takes two parameters:

* First, an anonymous function, which is the function that we want which should be created only once, whenever the component is re-evaluated.
* Second, a dependencies array, which contains the list of dependencies on which the first parameter depends.

_**Only use**** ****`useMemo()`**** ****for intensive tasks, like sorting a list, etc.**_

```jsx
import {useMemo} from 'react';
//...
const listItems = useMemo(() => {
  return [5, 3 , 1, 15, 9, 6, -4];
}, []);
//...
//...
<DemoComponent list={listItems} />
```

```jsx
//DemoComponnent
const Demo = ({list}) => {
  const sorted = useMemo(() => {
    return list.sort((a,b) => a-b);
  }, [list]);
}
```

### Class based Components

#### `shouldComponentUpdate`

For class-based components, we use the `shouldComponentUpdate(nextProps, nextState)` lifecycle hook. So we re-render the component only if it is required to re-render, otherwise it is discarded to re-render on the DOM.

```jsx
shouldComponentUpdate(nextProps, nextState) {
  console.log('shouldComponentUpdate');
  if(nextProps.persons !== this.props.persons) return true;
  return false;
}
```

In the above example, we can see that the component will update, i.e, return `true` for re-rendering only if there are changes in the `props`. If there are no differences b/w the old/previous props, the lifecycle hook returns `false` and the component will not re-render.

#### When should you optimize?

It might look tempting to always include a `shouldComponentUpdate` or `React.memo` to your class-based or functional components, but that is not the case.

In most cases, our components will have to re-render, because the parent component triggers an update, and mostly all the state/data is managed from the parent. So it's not wise to always include these optimization techniques.

If we include these functions, when the parent triggers a re-render, the child components, which implement the above mentioned optimization functions, will **unnecessarily run**, and in the end, will say there is a change and it need to re-render the component. So there is an unnecessary overhead, or a function is being run unnecessarily.

Performance optimization functions should be used carefully, only if it is absolutely needed, and when a component needs to re-render when the parent does not trigger a re-render (in most cases).

## Summary

The `render()` method does not immediately render to the "real" DOM. It is more of a suggestion of what the HTML should look like in the end.

![Summary of how the Real DOM updates](../.gitbook/assets/Screenshot\_2020-09-09\_at\_21.58.00.png)

React implements a `"virtual DOM"`, where the `render()` call creates a re-rendered "virtual DOM", and compares it with the `"old virtual DOM"`. If there are differences, only those differences are updated to the "real DOM", otherwise the "real DOM" is not touched.

Accessing the "real" DOM is very slow and this is something that we want to do as little as possible. Hence, React implements a `"virtual DOM"`, which makes sure the "real DOM" is only touched when needed.
