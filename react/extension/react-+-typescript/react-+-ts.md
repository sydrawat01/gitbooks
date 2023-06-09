---
description: Initialize CRA with TypeScript.
---

# 🔌 React + TS

## [Installation](https://create-react-app.dev/docs/adding-typescript/)

To start a new Create React App project with [TypeScript](https://www.typescriptlang.org/), you can run:

```bash
# using npx
npx create-react-app my-app --template typescript

# or, using yarn
yarn create react-app my-app --template typescript
```

In case you want to add typescript to an existing React project, you can add the following dependencies:

```bash
# using npm
npm install --save typescript @types/node @types/react @types/react-dom @types/jest

# or, using yarn
yarn add typescript @types/node @types/react @types/react-dom @types/jest
```

Next, rename any file to be a TypeScript file (e.g. `src/index.js` to `src/index.tsx`) and **restart your development server**!

Type errors will show up in the same console as the build one. You'll have to fix these type errors before you continue development or build your project. For advanced configuration, [see here](https://create-react-app.dev/docs/advanced-configuration).

## Components

Working with components in React using TypeScript.

### `Props`

There is a special way of defining components in a `.tsx` file, to use the `props` in the component. We could also define the prop type definition in the functional component parameters, but that would be cumbersome for various components that our app might have. So there is an alternative provided by '`@types/react`', which is available via the '`react`' library, and we use the '`FC`' component from that library.

{% code title="components/Todos.tsx" %}
```typescript
import {FC} from 'react';

const Todos: FC = (props) => {
  return (
    <>
      <div>
        {props.children}
      </div>
    </>
  );
}

export default Todos;
```
{% endcode %}

This code snippet above shows how we import and use the `FC` component function. In the end, it is a type definition for a function, which stands for '`Functional Component'`, and internally defines type definitions for `props` and `children` for a functional component.

### Type Annotations

Here, `React.FC` or `FC` is a generic type, which is defined by React on a functional component, and we can now have type definitions for our props in this component. We are not creating a new generic type, but are plugging in a concrete value for that internally used generic type before that type T defined by that `React.FC` type. We are doing this so that typescript does not infer the generic type here because here we are not calling some generic function with some parameters where the values could then be used to  for the inference, but rather we're defining a function and letting typescript know how it should treat this function internally that it should get some props defined by us and merge those with some base props like the children prop, which all functional components have.

```typescript
/* Use a generic type and explicitly set the concrete type
 * that should be used for the usage of this generic FC type.

 * Angular brackets are to tell React that we are using the generic
 * type FC, but with concrete values that is our own 'props' object,
 * where we describe our own props for this specifc functional component.

 * It is generic, because different functional components will have
 * different prop definitions.
 */
 
 import {FC} from 'react';
 
 const Todos: FC<{items: string[]}> = (props) => {
   // FC<{...}> merges our own prop definition to existing default prop
   // definition defined in React.FC
   //return...
 }
 
 export default Todos;
```

We use this, for better auto complete and more descriptive code in our editor, but it also has some other advantages, the main one being that the place where we use this `<Todos>` component. If we do not pass the `"items"` prop to the `<Todos>` component, Typescript will show a warning that we are missing props that we have defined in our functional component.

## Data Models

Since the props we pass to our functional component will not always be simple, they will carry more info with them , typically in an object, it is better and easier to read the code that is modular, and uses `type`, or `interface` or `class` models (type definitions) to describe the props for our `React.FC` type annotation that we add to the existing props. We can define these using `interface` as well, but here we will look at `class` models.

{% code title="models/todo.ts" %}
```typescript
class Todo {
  id: number;
  text: string;
  
  constructor(todoText) {
    this.text = todoText;
    this.id = Math.random();
  }
}

export default Todo;
```
{% endcode %}

This is the type of object that we will pass to our `<Todos>` component, where the **prop `'items'` will have an** **array of type '`Todo`'**, as defined in our model file `todo.ts`. In our `App` component, where we use our `Todos` component, we need to pass the _'items'_ prop with the data so that it can be rendered dynamically in our `<Todos>` component. The way we create a new data in the `items` prop is a little different, i.e, by calling the `constructor` of the class model we defined in `todo.ts` file.

{% tabs %}
{% tab title="Todos" %}
{% code title="components/ Todos.tsx" %}
```typescript
import {FC} from 'react';
import Todo from '../models/todo';

const Todos: FC<{items: Todo[]}> = () => {
  return(
    <ul>
      {props.items.map(items => <li key={item.id}>{item.text}</li>)}
    </ul>
  );
}

export default Todos;
```
{% endcode %}
{% endtab %}

{% tab title="App" %}
{% code title="App.tsx" %}
```typescript
import Todos from './components/Todos';
import Todo from './models/todo';

const App = () => {
  const data = [
    new Data('Learn React'),
    new Data('Learn Typescript'),
    new Data('Learn React + Typescript'),
  ];
  return(
    <>
      <Todos items={data}/>
    </>
  );
}
export default App;
```
{% endcode %}
{% endtab %}
{% endtabs %}

This makes our code well structured and difficult to misuse our component in any way. This ensures data cannot be manipulated, and that we get most of the errors during development instead of runtime. This is the benefit of using Typescript with React!

### Practice

Let's outsource the `<li>` element from the above example into it's own component `<TodoItem>`.

{% tabs %}
{% tab title="TodoItem" %}
{% code title="components/TodoItem.tsx" %}
```typescript
import {FC} from 'react';

const TodoItem: FC<{text: string}> = (props) => {
  return <li>{props.text}</li>
}

export default TodoItem;
```
{% endcode %}
{% endtab %}

{% tab title="Todos" %}
{% code title="components/Todos.tsx" %}
```typescript
import {FC} from 'react';
import TodoItem from './TodoItem';
import Todo from '../models/todo';

const Todos: FC<{items: Todo[]}> =(props) => {
  return(
    <ul>
      {props.items.map(item => (
        <TodoItem key={item.id} text={item.text}/>
      ))}
    </ul>
  );
}

export default Todos;
```
{% endcode %}
{% endtab %}
{% endtabs %}

## Forms

### `onSubmit` event

While submitting forms in React, we use the `event.preventDefault()` to prevent the browser default action when we submit a form. While handling the form submission, we need to provide the `"event"` parameter to the submit handler, in order to prevent the browser default action. Typescript does not know the type of the `"event"` that we pass to this submit handler function, and thus we use a special type, a type annotation provided by React, called **`"FormEvent"`**, which will tell typescript that this is an **event object type** which comes from the browser automatically when listening to a `onSubmit` event on a form. There are other events as well, like `"MouseEvent"`, which we could get on a `onClick` event.

### `useRef`

To get the input values of what the user has entered, we can either listen to every keystroke, like the `onChange` event, but here we will prefer to use the `useRef`, to point to an input and get the input value at once. We need to be explicitly about the type of data we will be storing in this `ref` because will show an error since we do not provide any type to the ref we create using the `useRef()` hook. To provide a type to the newly created ref, we can use generics.

`useRef()` is a generic type out of the box in our project. We can add the angled brackets and add a concrete type of `ref` we want to create in this instance. Now, the concrete value or the HTML element that we want to connect the `ref` with, is defined within these angled brackets.

Here, we want to use the `ref` with an `<input>` HTML element, so the concrete type for this element is a built-in type, called `HTMLInputElement`. All the DOM elements have a built-in type, which we can use to refer to them. For example, a `<button>` will have `HTMLButtonElement`, a `<p>` will have `HTMLParagraphElement`, et al.

So now we are making clear that the `ref` we are actually creating here, will actually be connected to an `HTMLInputElement`. But we also need to provide a starting value to it, since this `ref` could be assigned to another HTML element. Since we have no initial connection, we can make this as `null`.

#### Fetching the `ref` value

To get the value of the reference, we can use it in the same way we used to get the value in JSX files, but the autocomplete suggestion from our IDE adds a '**?**' after '**current**', like so:

`inputRef.current?.value`. This question mark added here signals to typescript that it tries to access `'value'` and if it succeeds, the value will be stored in the assigned variable, and if that fails, i.e, the connection between the `HTML element` and the `ref` is not established, it will store null in the assigned variable.

{% code title="components/NewTodoItem.tsx" %}
```typescript
import {useRef, FormEvent} from 'react';

const NewTodoItem = () => {
  const inputRef = useRef<HTMLInputElement>(null);
  
  const submitFormHandler = event: FormEvent => {
    event.preventDefault();
    const enteredVal = inputRef.current?.value;
    // The inferred type for enteredVal will be string | undefined,
    // if we hover over it in our IDE.
  }
  
  return (
    <form onSubmit={submitFormHandler}>
      <label htmlFor="todo">Enter a Todo</label>
      <input id="todo" type="text" ref={inputRef}/>
      <button>Add Todo</button>
    </form>
  );
}
```
{% endcode %}

In case we are sure that the connection has been established between the `ref` and the HTML element, we can then get the value of the ref by using the same syntax mentioned above, but just replacing the '**?**' with a '**!**' (exclamation). This tells typescript, that we are sure the `ref` will have a value that is not null / undefined. With that, if we hover over the variable where we store this value, the inferred type will be a `string`, and nothing else.

```typescript
const enteredVal = inputRef.current!.value;
// Inferred type for enteredVal will be string, if we hover
// over it in our IDE.
```

The question mark (?) and exclamation (!) operators are not specific to React, but they are used in typescript for checking if a value is null / undefined, or has a definite value other than null / undefined.

## Function Props

Moving on with the same example as above, we want to update the `Todos` list with the new `Todo` added by the user via the `NewTodoItem` component. But first, we need a function, that will send this `enteredVal` to the place we are using the `NewTodoItem` component, i.e, the `App` component. We do this in the same way we used to before,  by sending a function as a prop to `NewTodoItem` component, which adds the `enteredVal` to the list of "todos" managed in the `App` component. We will have to define the prop type in the `NewTodoItem` component, as we did before.

{% tabs %}
{% tab title="NewTodoItem" %}
{% code title="components/NewTodoItem.tsx" %}
```typescript
import {useRef, FormEvent, FC} from 'react';

const NewTodoItem: FC<{onAddTodo: (text: string) => void}> = (props) => {
  const inputRef = useRef<HTMLInputElement>(null);
  
  const submitFormHandler = (event: FormEvent) => {
    event.preventDefault();
    const enteredVal = inputRef.current!.value;
    props.onAddTodo(enteredVal);
  }
  
  return(
    <form>
     //...
    </form>
  )
}

export default NewTodoItem;
```
{% endcode %}
{% endtab %}

{% tab title="App" %}
{% code title="App.tsx" %}
```typescript
...
import NewTodoItem from './components/NewTodoItem';
...
...
const App = () => {
  const addTodoHandler = (text: string) => {
    //... add new todo to list of todos
    // todos list to be managed by state.
  }
  return(
    <NewTodoItem onAddTodo={addTodoHandler} />
  )
}

```
{% endcode %}
{% endtab %}
{% endtabs %}

## State

We want to tell typescript that the type of data managed by the `useState()` hook in our case would be **an array of `Todo` types, that we defined earlier in the `models/` folder, inside of the `todo.ts` file**. We can do this, because the `useState()` is a generic type again, and we can give it a concrete type value. We would still need to give the default value to `useState()` hook when we define it, and store the returned values in a de-structured array, just like before.

{% code title="App.tsx" %}
```typescript
import {useState} from 'react';

import NewTodoItem from './components/NewTodoItem';
import Todos from './components/Todos';

import Todo from './models/todo';

const App = () => {
  const [todos, setTodos] = useState<Todo[]>([]);
  
  const addTodoHandler = (text: string) => {
    const newTodo = new Todo(text);
    setTodos(prevTodos => prevTodos.concat(newTodo));
  } ;
  
  return(
    <>
      <NewTodoItem onAddTodo={addTodoHandler}/>
      <Todos items={todos}/>
    </>
  );
};

export default App;
```
{% endcode %}

### Removing a Todo Item

We can remove a `Todo` item from the todo list by using the following code snippets, which in the end do not need to use the `MouseEvent` from the React library. The problem we will face here, is that we will create a **prop chain**, by sending the delete function prop from `<TodoItem>` to `<Todos>` and then finally `<App>,` where we manage the state of all `Todo` items.

{% tabs %}
{% tab title="TodoItem" %}
{% code title="components/TodoItem.tsx" %}
```typescript
import {FC} from 'react';

const TodoItem: FC<{
  text: string;
  onRemoveTodo: () => void;
}> = (props) => {
  return <li onClick={props.onRemoveTodo}>{props.text}</li>
}

export default TodoItem;
```
{% endcode %}
{% endtab %}

{% tab title="Todos" %}
{% code title="components/Todos.tsx" %}
```typescript
import {FC} from 'react';
import Todo from '../models/todo.ts';
import TodoItem from './TodoItem';

const Todos: FC<{
  items: Todo[];
  removeTodo: (id: number) => void;
}> = (props) => {
  return (
    <ul>
      {props.items.map(item => (
        <TodoItem 
          key={item.id}
          text={item.text}
          onRemoveTodo={props.removeTodo.bind(null, item.id)}
        />
      ))}
    </ul>
  );
}

export default Todos;
```
{% endcode %}
{% endtab %}

{% tab title="App" %}
{% code title="src/App.tsx" %}
```typescript
import {useState} from 'react';
import Todos from './components/Todos';
import NewTodoItem from './components/NewTodoItem';

import Todo from './models/todo';

const App = () => {
  const [todos, setTodos] = useState<Todo[]>([]);
  
  const addTodoHandler = (text: string) => {
    const newTodo = new Todo(text);
    setTodos(prevTodos => prevTodos.concat(newTodo));
  }
  
  const removeTodoHandler = (id: number) => {
    setTodos(prevTodos => prevTodos.filter(todo => todo.id !=== id));
  }
  
  return (
    <>
      <NewTodoItem onAddTodo={addTodoHandler}/>
      <Todos items={todos} removeTodo={removeTodoHandler}/>
    </>
  );
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

## Context API

We are creating prop chains when passing functions as props for adding and remove items from the todo list. So, the basic solution to avoid prop chains is to use the React Context API.

We will start off by creating a new file called '`todos-context.tsx`' inside a new folder, called `store/`,  which will contain our context object, as well as the context provider for our `<App>`

{% code title="store/todos-context.tsx" %}
```typescript
import {createContext, useState, FC} from 'react';
import Todo from '../models/todo';

type TodosContextTypeObj = {
  items: Todo[];
  addTodo: (text: string) => void;
  removeTodo: (id: number) => void;
}

export const TodosContext = createContext<TodosContextTypeObj>({
  items: [],
  addTodo: () => {},
  removeTodo: (id: number),
});

export const TodosContextProvider: FC = (props) => {
  const [todos, setTodos] = useState<Todo[]>([]);
  
  const addTodoHandler = (text: string) => {
    const newTodo = new Todo(text);
    setTodos(prevTodos => prevTodos.concat(newTodo));
  }
  
  const removeTodoHandler = (id: number) => {
    setTodos(prevTodos => prevTodos.map(item => item.id !== id));
  }
  
  const contextValue: TodosContextTypeObj = {
    items: todos,
    addTodo: addTodoHandler,
    removeTodo: removeTodoHandler,
  }
  
  return (
    <TodosContext.Provider value={contextValue}>
      {props.children}
    </TodosContext.Provider>
  );
}
```
{% endcode %}

Some important concepts from the above code:

* We use the type alias `TodosContextTypeObj`, to define the structure of the context object, which we will be using across our app. We do this for better linting, as our IDE will point out to the places where we misuse the context object types, or declare them incorrectly, or in cases where we add functions to the context, but forget ot give them the appropriate parameters.
* The same type alias is used to define the structure of the context value, which we provide to the context provider.
* In case there is any error, in defining or using the context object values, the IDE will point out the specific place where we have misused the context object property.

With the help of context API, we remove prop chaining from the app, and thus we can then remove the prop type definitions from the components, and use the context object to then use all the props from within this context.

{% tabs %}
{% tab title="App" %}
{% code title="src/App.tsx" %}
```typescript
import Todos from './components/Todos';
import NewTodoItem from './components/NewTodoItem';

import {TodosContextProvider} from './store/todos-context';

const App = () => {
  return (
    <TodosContextProvider>
      <NewTodoItem />
      <Todos />
    </TodosContextProvider
  );
}

export default App;
```
{% endcode %}
{% endtab %}

{% tab title="Todos" %}
{% code title="components/Todos.tsx" %}
```typescript
import {useContext, FC} from 'react';
import TodoItem from './TodoItem';

import {TodosContext} from '../store/todos-context';

const Todos: FC = () => {
  const todosCtx = useContext(TodoContext);
  const todoList = todosCtx.items.map(todo => (
    <TodoItem 
      key={todo.id}
      text={todo.text}
      onRemoveTodo={todosCtx.removeTodo.bind(null, todo.id)}
    />
  ));
  const emptyMsg = <h1>You're all caught up! :)</h1>
  
  return (
   <ul>
     {todosCtx.items.length === 0 ? emptyMsg : todoList}
   </ul>
  );
}
```
{% endcode %}
{% endtab %}

{% tab title="NewTodoItem" %}
{% code title="components/NewTodoItem.tsx" %}
```typescript
import {useRef, useContext, FC, FormEvent} from 'react';
import {TodosContext} from '../store/todos-context';

const NewTodoItem: FC = () => {
  const todosCtx = useContext(TodosContext);
  const inputRef = useRef<HTMLInputElement>(null);
  
  const submitFormHandler = (event: FormEvent) => {
    event.preventDefault();
    const enteredValue = inputRef.current!.value;
    if(enteredValue.trim().length === 0) return;
    todosCtx.adddTodo(enteredValue);
  }
  
  return (
    <form onSubmit={submitFormHandler}>
      <label htmlFor="new-todo">
        <h1>Enter a Todo Item<h1>
      </label>
      <input id="new-todo" type="text" ref="inputRef" />
      <button>Add Todo</button>
    </form>
  );
}

export default NewTodoItem;
```
{% endcode %}
{% endtab %}
{% endtabs %}

The final project can be found [here](https://codesandbox.io/s/typescript-todo-app-yu6hu). The same project is also available with [`Redux-Toolkit`](https://redux-toolkit.js.org/) [here](https://codesandbox.io/s/rtk-ts-todo-kvcfz).
