---
description: How to create and use custom hooks.
---

# ⚓️🪝Custom Hooks

## What are custom hooks? Why do we need them?

We need custom hooks, where we have a function component, which uses built-in hooks, like `useState` or `useEffect`, and has redundant code in it. Since we cannot use built-in hooks with functions, we create custom hooks to use these built-in hooks to be used in the function component \[or a custom hook, now]. It is basically to reduce redundant code where we are dependent upon built-in hooks.

Custom hooks are functions where we can outsource `stateful` logic into `re-usable functions`. But unlike "regular functions", custom hooks can use other React hooks and React state.

The naming convention is to be followed while creating custom hooks.

* Store all custom hooks in a `hooks/` folder, at root level in `/src`
* The file name for the custom hook must be `use-<hook_name>.js`
* The hook function should be named in _camelCase_, like `useHookName`
* Import the hook like we did other default built-in hooks.

When calling a custom hook, the state of that custom hook is then tied to the functional component in which we call that custom hook. Every component in which we use our custom hook, a fresh set of state is bound to each component, which is different from the other components.

We need to return something from our custom hooks. It can be a variable of primitive data type, can be an array, an object, etc.

### Example

Consider the following `useCounter()` custom hook example:

#### Creating the hook

```jsx
import {useState, useEffect} from 'react';

export const useCounter = () => {
  const [counter, setCounter] = useState(0);

  useEffect(()=>{
    const interval = setInterval(()=>{
      setCounter((prevCounter) => prevCounter + 1);
    }, 1000)

    return () => {
      clearInterval(interval);
    }
  }, []);

  return counter;
}
```

#### Using this custom hook

{% code title="ForwardCounter.js" %}
```jsx
import Card from './Card';
import {useCounter} from './hooks/use-counter';

const ForwardCounter = () => {
  const counter = useCounter();

  return <Card>{counter}</Card>;
}

export default ForwardCounter;
```
{% endcode %}

{% code title="App.js" %}
```jsx
import React from 'react';
import ForwardCounter from './components/ForwardCounter';
import BackwardCounter from './components/BackwardCounter';

const App = () => {
  return(
    <>
      <ForwardCounter />
      <BackwardCounter />
    </>
  )
}
```
{% endcode %}

### Configuring the custom hook

The above logic for the custom hook only solves the issue for the `ForwardCounter` component. To have the same custom hook count backwards, i.e, for the `BackwardCounter`, we need to configure our custom hook. For this, we will use a flag, i.e, a boolean, to check if we are counting forwards, for the `ForwardCounter` component, or backwards for the `BackwardCounter` component.

{% code title="use-counter.js" %}
```jsx
import {useState, useEffect} from 'react';

export const useCounter = (forward = true) => {
  const [counter, setCounter] = useState(0);

  useEffect(()=>{
    const interval = setInterval(() => {
      if(forward)
    setCounter((prevCounter) => prevCounter + 1);
      else
    setCounter((prevCounter) => prevCounter - 1);
    }, 1000)

    return () => {
      clearInterval(interval)
    }
  }, [])

  return counter;
}
```
{% endcode %}

We set a boolean flag `forward` which is set to `true` by default. This value is checked in the `useEffect()` where we update the state of the counter, whether we need to increment or decrement the counter.

The `ForwardCounter` component remains the same, but here is the updated `BackwardCounter` component.

{% code title="BackwardCounter.js" %}
```jsx
import React from 'react';
import Card from './Card';
import {useCounter} from '../hooks/use-counter';

const BackwardCounter = () => {
  const counter = useCounter(false);

  return <Card>{counter}</Card>;
}

export default ForwardCounter;
```
{% endcode %}

### Custom HTTP hook for `GET` and `POST` requests

To check out the whole project:

* [Codesandbox.io](https://codesandbox.io/s/firebase-tasks-hbzwg)
* [Github project](https://github.com/sydrawat/react/tree/custom-hooks)

To check out the working project, visit [codesandbox.io](https://hbzwg.csb.app/)

{% code title="hooks.use-http.js" %}
```jsx
import {useState, useCallback} from 'react';

export const useHttp = () => {
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState(null);

  const sendRequest = useCallback( async(requestConfig, applyData) => {
    setIsLoading(true);
    setError(null);
    try {
      const response = await fetch(requestConfig.url, {
        method: requestConfig.method ? requestConfig.method : 'GET',
        headers: requestConfig.headers ? requestConfig.headers : {},
        body: requestConfig.body ? JSON.stringify(requestConfig.body) : null
      })

      if (!response.ok) {
    throw new Error('Request failed!');
      }
      const data = await response.json();
      applyData(data);
    } catch (error) {
      setError(error.message || 'Something went wrong!');
    }
    setIsLoading(false);
  }, [])

  return {
    isLoading,
    error,
    sendRequest
  }
}
```
{% endcode %}

{% code title="App.js" %}
```jsx
import React, { useEffect, useState } from 'react';
import { useHttp } from './hooks/use-http';
import Tasks from './components/Tasks/Tasks';
import NewTask from './components/NewTask/NewTask';

function App() {
  const [tasks, setTasks] = useState([]);
  const { isLoading, error, sendRequest: fetchTasks } = useHttp();
  useEffect(() => {
    const transformTasks = (taskObj) => {
      const loadedTasks = [];
      for (const taskKey in taskObj) {
        loadedTasks.push({
          id: taskKey,
          text: taskObj[taskKey].text,
        });
      }
      setTasks(loadedTasks);
    };
    fetchTasks(
      {
        url: 'https://custom-hook-8da32-default-rtdb.firebaseio.com/tasks.json',
      },
      transformTasks
    );
  }, [fetchTasks]);

  const taskAddHandler = (task) => {
    setTasks((prevTasks) => prevTasks.concat(task));
  };

  return (
    <React.Fragment>
      <NewTask onAddTask={taskAddHandler} />
      <Tasks
        items={tasks}
        loading={isLoading}
        error={error}
        onFetch={fetchTasks}
      />
    </React.Fragment>
  );
}

export default App;
```
{% endcode %}

{% code title="NewTask.js" %}
```jsx
import { useHttp } from '../../hooks/use-http';
import Section from '../UI/Section';
import TaskForm from './TaskForm';

const NewTask = ({ onAddTask }) => {
  const { isLoading, error, sendRequest: sendTask } = useHttp();

  const transformTask = (taskText, taskObj) => {
    const generatedId = taskObj.name; // firebase-specific => "name" contains generated id
    const createdTask = { id: generatedId, text: taskText };

    onAddTask(createdTask);
  };

  const enterTaskHandler = async (taskText) => {
    sendTask(
      {
        url: 'https://custom-hook-8da32-default-rtdb.firebaseio.com/tasks.json',
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: { text: taskText },
      },
      transformTask.bind(null, taskText)
    );
  };

  return (
    <Section>
      <TaskForm onEnterTask={enterTaskHandler} loading={isLoading} />
      {error && <p>{error}</p>}
    </Section>
  );
};

export default NewTask;
```
{% endcode %}
