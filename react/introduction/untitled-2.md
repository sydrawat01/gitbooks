---
description: To render or not to render?
---

# ☝🏻 Conditional Rendering

## [Conditional Rendering](https://reactjs.org/docs/conditional-rendering.html)

Suppose we want to render the persons data conditionally, say on the click of a button that toggles the data.

{% code title="App.js" %}
```jsx
state = {
  //...
  //...
  show: false // initially do not show anything
}

togglePersonsHandler = () => {
  const currVal = this.state.show
  this.setState({
    show: !currVal
  });
}

render() {
  let conditionalPersons = null;
  if(this.state.show) {
    conditionalPersons = (
      <div>
        <Person .../>
        <Person .../>
        <Person .../>
      </div>
    )
  } 
  return (
    <div>
      <button onClick={this.togglePersonsHandler}> Toggle </button>
        {conditionalPersons}
    </div>
  );
}
```
{% endcode %}

## [Outputting Lists](https://reactjs.org/docs/lists-and-keys.html)

Instead of manually adding multiple `<Person />` components to our app, we can use array methods to output a list of `<Persons />` components.

In the same code where we rendered the content conditionally, i.e, `conditionalPersons`:

```jsx
let conditionalPersons = null
if(this.state.show) {
  conditionalPersons = (
    <div>
      {this.state.persons.map(item=> {
        return <Person name={item.name} age={item.age} />
      })}
    </div>
  );
}
render() {
  return (
    <button onClick={this.togglePersonsHandler}> Toggle </button>
    {conditionalPersons}
  );
}
```

## Virtual DOM

React has a feature where it compares the current DOM with a virtual DOM, which is a visualization of what would be rendered in the future, compared to the changes that could be made in the past DOM.

In lists, it needs to be known which exact elements change. By default, this will render the whole list. For very large list of components, this is an inefficient way.

Hence we need to assign a "key" property to each child in the list so that React can keep track of each individual element inside the list, so that it can compare the different elements and only render those to the actual DOM, which have actually changed, and not the whole list.

Example:

```jsx
state = {
  persons: [
    {id: 'at123', name: 'Ham', age: 32},
    {id: '321at', name: 'Bot', age: 33},
    {id: '33rxjs', name: 'Ver', age: 25}
  ],
  show: false
}
```

**NOTE:** We can always use the index of the array as key, but it is better to use a separate property called `id` so that it is easier for us as well to identify the element in the list.

```jsx
//deletePersonsHandler
const deletePersonsHandler = (index) => {
  const persons = [...this.state.persons];
  persons.splice(index, 1);
  this.setState({
    persons: persons
  });
}

//conditional rendering
render() {
  let conditionalPersons = null
  if(this.state.show) {
    conditionalPersons = (
      <div>
        {this.state.persons.map((item, index)=> {
          return <Person 
            click={this.deletePersonsHandler.bind(this, index)}
            name={item.name}
            age={item.age}
            key={item.id}
          />
        })}
      </div>
    )
  }
  return (
    <div>
      <button click={togglePersonsHandler}>Toggle</button>
      {conditionalPersons}
    </div>
  );
}
```

The `id` being linked to `key` in the list when it is conditionally being rendered, is better for react to keep track of which element in the list needs re-rendering based on the comparison of the actual and the virtual DOM.

The `deletePersonsHandler` is an `eventHandler` that deletes the item from the list when it is clicked. This is in turn also removed from the state.

## Flexible List

In the below example, we will be changing the name of the `Person` component using another `eventHandler` that will work only when it matches with the `id` of that particular person, where we are renaming it, i.e, in the text field of that `Person` component:

```jsx
{this.state.persons.map((item, index) => {
  return <Person 
    name={item.name}
    age={item.age}
    click={this.deletePersonsHandler(this, index)}
    change={(event)=> this.nameChangeHandler(event, item.id)}
  />
})}

//nameChangeHandler
nameChangeHandler = (event, id) => {
  const personIdx = this.state.persons.findIndex(el=>el.id === id);
  const person = {...this.state.persons[personIdx]};
  person.name = event.target.value;
  const persons = [...this.state.persons];
  persons[personIdx] = person;
  this.setState({
    persons: persons
  });
}
```

{% code title="Person.js" %}
```jsx
const Person = ({...,name, change, children}) => {
  return(
    <div>
      //...
      //...
      <input type="text" onChange={change} defaultValue={name}/>
      {children}
    </div>
  );
}
```
{% endcode %}

With this, we have a truly flexible component, which renders content conditionally, and only that component from the list whose state change, is re-rendered, with the `key` property linked to the list element.

NOTE: To get the `event` object from the place where the changes to the component happens, we need to define the event as an anonymous function's prop, like so:

```jsx
<Person 
  change={(event) => {this.nameChangeHandler(event, item.id)}}
/>
```
