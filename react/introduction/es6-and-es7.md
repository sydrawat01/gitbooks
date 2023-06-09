---
description: Basic features of modern JavaScript syntax useful to know for learning React.
---

# 📜 ES6 & ES7

## `let` & `const`

[Read more about `let`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let) [Read more about `const`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)

`let` and `const` basically replace `var` . You use `let` instead of `var` and `const` instead of `var` if you plan on never re-assigning this "variable" (effectively turning it into a constant therefore).

## ES6 Arrow Functions

[Read more](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow\_functions)

Arrow functions are a different way of creating functions in JavaScript. Besides a shorter syntax, they offer advantages when it comes to keeping the scope of the `this` keyword.

Arrow function syntax may look strange but it's actually simple.

```javascript
function callMe(name) {
  console.log(name);
}
```

which you could also write as:

```javascript
const callMe = function(name) {
  console.log(name);
}
```

becomes:

```javascript
const callMe = (name) => {
  console.log(name);
}
```

#### Important:

When having **no arguments**, you can use the following shortcut:

```javascript
const callMe = name => {
  console.log(name);
}
```

When **just returning a value**, you can use the following shortcut:

```javascript
const callMe = name => name;
```

That's equal to:

```javascript
const callMe = name => {
  return name;
}
```

## Exports & Imports

In React projects (and actually in all modern JavaScript projects), you split your code across multiple JavaScript files - so-called modules. You do this, to keep each file/ module focused and manageable.

To still access functionality in another file, you need `export` (to make it available) and `import` (to get access) statements.

You got two different types of exports: **default** (unnamed) and **named** exports: default => `export default ...;` named => `export const someData = ...;`

You can import **default exports** like this:

```javascript
 import someNameOfYourChoice from './path/to/file.js';
```

The variable name `someNameOfYourChoice` is totally up to you.

**Named exports** have to be imported by their name:

```jsx
import { someData } from './path/to/file.js';
```

A file can only contain one default and an unlimited amount of named exports. You can also mix the one default with any amount of named exports in one and the same file.

When importing **named exports**, you can also import all named exports at once with the following syntax:

```javascript
import * as upToYou from './path/to/file.js';
```

`upToYou` is - well - up to you and simply bundles all exported variables/functions in one JavaScript object. For example, if you `export const someData = ... (/path/to/file.js)` you can access it on `upToYou` like this: `upToYou.someData`

## Classes

Classes are a feature which basically replace constructor functions and prototypes. You can define blueprints for JavaScript objects with them.

Like this:

```javascript
class Person {
  constructor() {
    this.name = 'Sid';
  }
}

const person = new Person();
console.log(person.name); // prints 'Sid'
```

In the above example, not only the class but also a property of that class (=> `name` ) is defined. They syntax you see there, is the "old" syntax for defining properties. In modern JavaScript projects, you can use the following, more convenient way of defining class properties:

```javascript
class Person {
  name = 'Sid'
}

const person = new Person();
console.log(person.name); // prints 'Sid'
```

You can also define methods. Either like this:

```jsx
class Person {
  name = 'Sid';
  printMyName() {
    console.log(this.name);//`this` is required to refer to the class
  }
}

const person = new Person();
person.printMyName();
```

Or like this:

```jsx
class Person {
  name = 'Sid';
  printMyName = () => {
    console.log(this.name);
  }
}

const person = new Person();
person.printMyName();
```

The second approach has the same advantage as all arrow functions have: The `this` keyword doesn't change its reference.

You can also use **inheritance** when using classes:

```jsx
class Human {
  species = 'human';
}

class Person extends Human {
  name = 'Sid';
  printMyName = () => {
    console.log(this.name)
  }
}

const person = new Person();
person.printMyName();
console.log(person.species);// prints 'human'
```

## Spread & Rest Operator

The spread and rest operators actually use the same syntax: `...`

Yes, that is the operator - just three dots. It's usage determines whether you're using it as the spread or rest operator.

#### Using the Spread Operator:

The spread operator allows you to pull elements out of an array (=> split the array into a list of its elements) or pull the properties out of an object. Here are two examples:

```javascript
const oldArray = [1, 2, 3];
const newArray = [...oldArray, 4, 5];// This is now [1, 2, 3, 4, 5]
```

Here's the spread operator used on an object:

```javascript
const oldObj = {
  name: 'Sid'
};

const newObj = {
  ...oldObj,
  age: 25
};
```

`newObj` would then be:

```javascript
{
  name: 'Sid',
  age: 25
}
```

The spread operator is extremely useful for cloning arrays and objects. Since both are [reference types (and not primitives)](https://www.youtube.com/watch?v=9ooYYRLdg\_g\&feature=youtu.be), copying them safely (i.e. preventing future mutation of the copied original) can be tricky. With the spread operator you have an easy way of creating a (shallow!) clone of the object or array.

#### Using the Rest Operator:

The rest operator is used to merge a list of function arguments into an array.

```javascript
function sortArgs(...args) {
  return args.sort();
}
```

Since `args` are now of type `Array`, they can be sorted by using the `sort()` built in function.

Example:

```javascript
const restEg = (...args) => {
  return args.filter(el => el%2==0);
}

console.log(restEg(1,2,3,4,5,6,7,8,9,10)); //[2,4,6,8,10]
```

## De-structuring

De-structuring allows you to easily access the values of arrays or objects and assign them to variables.

Here's an example for an array:

```javascript
const arr = [1, 2, 3];
const [a, b] = arr;
console.log(a);// prints 1
console.log(b);// prints 2
console.log(arr);// prints [1, 2, 3]
```

And here for an object:

```javascript
const myObj = {
  name: 'Sid',
  age: 25
}

const {name} = myObj;
console.log(name);// prints 'Sid'
console.log(age);// prints undefined
console.log(myObj);// prints {name: 'Sid', age: 25}
```

De-structuring is very useful when working with function arguments. Consider this example:

```javascript
const printName = (personObj) => {
  console.log(personObj.name);
}

printName({name: 'Sid', age: 25});
```

Here, we only want to print the name in the function but we pass a complete person object to the function. Of course this is no issue but it forces us to call `personObj.name`

```javascript
const printName = ({name}) => {
  console.log(name);
}

printName({name: 'Sid', age: 25}); // prints 'Sid'
```

We get the same result as above but we save some code. By de-structuring, we simply pull out the `name` property and store it in a variable/ argument named which we then can use in the function body.

## Refreshers

### Reference & Primitive data types

Reference data types are `arrays` and `objects`. In this type, there are pointers to the original data are copied in the memory, so a direct copy of the data isn't made.

Example:

```javascript
const Person1 = {
  name: 'Sid'
}

const Person2 = Person1;
Person1.name = 'Max';

console.log(Person2.name); // 'Max'
```

So, any changes to the original data type will be reflected in the copy of that "referenced" array or object.

Primitive data types are `boolean`, `string`, `number`. In this type of data, a direct copy of the data is made. Any changes made to the original data type are not reflected in the copied data type, they are both independent of each other.

Example:

```javascript
a = 'Siddharth';
b = a;
a = 'Rahul';
console.log(a); // 'Rahul'
console.log(b); // 'Siddharth'
```

### Array Functions

* **MAP** This array function returns a new array.

```javascript
const numbers = [1,2,3,4,5,6];
const double = numbers.map(item => item*2);
console.log(numbers); //[1,2,3,4,5,6]
console.log(double); //[2,4,6,8,10,12]
```

* **FILTER** This array function returns a new array.

```javascript
const numbers = [1,2,3,4,5,6,7,8,9,10];
const even = numbers.filter(item => item % 2 == 0);
console.log(numbers); //[1,2,3,4,5,6,7,8,9,10]
console.log(even); //[2,4,6,8,10]
```

* **REDUCE** This returns a single value.

```javascript
const numbers = [1,2,3,4];
const sum = numbers.reduce((accumulator, currVal, currIdx, numbers) => {
    return accumulator + currentVal;
  }, 0);
console.log(sum); //10

const newSum = numbers.reduce((acc,currVal) => {
    return acc+currVal;
  }, 5);
console.log(newSum); //15
```

* **SLICE** This returns a shallow copy of the portion of the array into a new array from "START" to "END" index that is specified in the limits. \[END index is NOT INCLUDED]

Also, if the END index is not specified, the slice operation will add a default end index, that is the last index of the referred array.

```javascript
const animals = ['cat', 'dog', 'cow', 'duck'];
console.log(animals.slice(2)); // ['cow', 'duck']
console.log(animals.slice(1,3)); // ['dog','cow']
console.log(animals.slice(1, animals.length)); // ['dog', 'cow', 'duck']
```

* **SPLICE** This array function changes the context of array by removing/replacing the existing elements and/or adding the new elements specified IN PLACE \[same array is mutated].

```javascript
const months = ['Jan', 'Mar', 'Jun'];

months.splice(1, 0, 'Feb'); //['Jan' ,'Feb', 'Mar', 'Jun']
// insert 'Feb' at index 1, replace 0 elements.

months.splice(3, 0, 'Apr', 'May');
//['Jan' ,'Feb', 'Mar', 'Apr', 'May', 'Jun']
```
