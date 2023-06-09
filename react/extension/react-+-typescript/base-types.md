---
description: Digging deeper into TypeScript.
---

# 🪴 Base Types

## Primitive Types

In JavaScript, `null`, `undefined`, `boolean`, `number`, `string`, etc. are some of the important primitive data types. Let's look at the core basic primitive types in Typescript:

### Numbers

It is important here that the type definition for a 'number' starts with a small-case 'n'. If we use 'Number' instead of 'number', we would be pointing to the `object` Number, and not the `type` 'number'. Same goes with 'string' and 'boolean'.

```typescript
let age: number = 24; //initial value
age = 27; // correct
age = '26'; //wrong, will give a warning!
```

### Strings

Please use 'string' instead of 'String' for reasons stated above.

```typescript
let userName: string = 'Sid'; //initial value;
userName = 'Dennis'; //correct
userName = 25; //wrong, will give a warning!
```

### Boolean

Please use 'boolean' instead of 'Boolean' for reasons stated above.

```typescript
let isMale: boolean = true; //initial value;
isMale = false; //correct
isMale = 'true'; //wrong, will give a warning!
isMale = 1; //wrong, will give a warning!
```

## Complex Types

Moving ahead from the primitive data types, we will have a look at the complex data types, mainly: `arrays` and `objects`.

### Arrays

```typescript
let hobbies: string[]; //array of strings
hobbies = ['hello', 'I', 'am', 'Sid']; //correct
hobbies = ['hello', 'hi', 25, true]; //wrong, will give a warning!

let boolArr: boolean[]; //array of boolean values
boolArr = [true, false, true, true]; //correct
boolArr = ['hi', 24]; //wrong, will give a warning!

let ages: number[]; //array of numbers
ages = [23, 25, 27]; //correct
ages = ['hi', true]; //wrong, will give a warning!
```

### Objects

Typescript has a special type, called `"any"` , which allows us to declare variables without telling typescript what type of values we want in that variable. We do not need to explicitly provide this type, but we can if we would like to. But this actually defeats the purpose of using Typescript in the first place.

```typescript
let person: any;

person = {
  name: 'Sid',
  age: 26
}
```

We can define the **`"object type definition`**" for an object in Typescript:

```typescript
let person: {
  name: string,
  age: number,
}

person = {
  name: 'Sid',
  age: 25,
} // correct

person = {
  isMale: true, // not present in object type definition!
} // wrong, will show a warning!
```

To store an array of objects with object type definition, we can do it as shown below:

```typescript
let persons: {
  name: string,
  age: number,
}[];

persons = [
  {name: 'Sid', age: 25},
  {name: 'Dennis', age: 27},
]
```

## Type Inference

By default, if we do not provide the type of variable we are initializing, Typescript tries to infer the type by itself, and gives a warning if we assign a different type to that variable, without is explicitly stating the type of variable we are defining.

```typescript
let title = 'Eternal Sunshine of the Spotless Mind';
//typescript infers the type of "title"  here as string.
// if we try to assign a number to title, it will give a warning
title = 25; //wrong, a warning is shown!
```

## Union Types

A `union type` is a type definition that allows more than one type to be assigned to a variable.

```typescript
let title: string | number = 'Eternal Sunshine of the Spotless Mind';

title = 25; // correct!
title = true; //wrong, will give a warning!
```

## Type Aliases

We use type aliases to avoid type definition duplication. We can assign and use aliases to type definitions to avoid such code duplication. We define an alias using the `type` keyword, which does not exist in standard Javascript, but is defined in Typescript. We then provide a type name, which we then provide type definition to. This makes our code more concise and easier to maintain.

```typescript
type Person = {
  name: string,
  age: number,
}

let people: Person[];
let person: Person;
```

## Functions & Function Types

### Functions returning values

Just as we provided type definition for variables, we can provide type definitions for parameters of a function. Based on these parameter type definitions, Typescript can **`infer`** the return type, and internally set the function return type definition to that type.

Consider the following code snippets, where both the functions have the same return type:

```typescript
//explicitly define return type as "number"
const addExplicit = (a: number, b: number): number => a + b;

//let typescript infer the return type definition from param types
const addInfered = (a: number, b: number) => a + b;
```

### `"void"` type definition

Consider a function where we just log some data to the console, and do not return any value from the function. In that case, Typescript will infer a special type called `"void"`. We can explicitly define the function type definition as well with `"void"`. _Void_ basically means it is comparable to null and undefined, but it is limited to be used only to functions, which ideally means that the function never returns anything.

```typescript
// this function takes in a param 'val' of type 'any'
// and does not return anything, which means it will have a type
// definition of type 'void', infered by typescript.
const printValInfered = (val: any) => {
  console.log('value = ', val);
}

// explicit 'void' type definition for functions that do not
// return anything.
const printValExplicit = (val: any): void => {
  console.log('value = ', val)
}


```

## Generics

Consider the following example, where we insert a value at the beginning of an array, without altering the original array, and we return a new array with the new value inserted at the beginning of the original array:

```typescript
const insertAtBeginning = (array: any[], value: any): any[] => {
  const newArray = [value, ...array];
  return newArray;
}
```

### Problem

It is a useful helper function, which takes in any type of array and a value, and inserts the value at the beginning of the array. But the issue here is, we want the array and value to be of the same type, but at the same time, this helper function should be flexible enough to allow other types also to be used in this function.

> Ideally, it would be useful if this function worked with arrays of number, strings, etc, but not a mixture of arrays with strings and numbers (which is what is happening with the type definition of "any").

Since Typescript does not know the type of values inside the array, we could call a string function on an array value that is of type number. This is wrong, since it would give an error during runtime, but typescript does not know this, and hence does not throw an error.

```typescript
const demoArr = [1, 2, 3, 4];
const demoVal = -1;
const updatedArr = insertAtBeginning(demoArr, demoVal);//[-1, 1, 2, 3, 4]

//using split() string fn on the first value in the returned array
//which consists of only number types, and not string types.
// Will give an error on runtime, but typescript does not.
console.log(updatedArr[0].split(''));
```

Hence, we have `Generics`, that will help make the `insertAtBeginning` function into a generic function.

### Using Generics

For using generics to solve the above mentioned problem, we have a special syntax. Before the start of the parameter brackets, and after the function name, we add `angular brackets` or `<>`. Inside these brackets, we will define a generic type that will only be available within this function. Typically it's called `"T"`, but we can use any identifier. Now we can use this type in our function, including the parameter list. Have a look below:

```typescript
const dummyFn1 = <T>(array: T[], value: T) => { //... }
const dummyFn2<T>(array: T[], value: T) { //... }
```

With this, we tell Typescript that the type of `array` is `T` (which can be array of strings or numbers or any, etc.), and the `value` should also have the same type (i.e, of type number or string or any), and finally the function will also have the same return type `T`, which should again be of the same type as array and value.

```typescript
const insertAtBeginning = <T>(array: T[], value: T) => {
  const newArr = [value, ...array ];
  return newArr;
}

const numArr = insertAtBeginning([1, 2, 3], 0);//[0, 1, 2, 3]
const strArr = insertAtBeginning(['bbb', 'cc'], 'a');//['a', 'bbb', 'cc']

/* -------------------------------------------------------------------
 * With numArr, 'insertAtBeginning' will have return type T as array[]
 * of numbers. Array will have type of array[] of numbers, value will
 * have type of number.
 * -------------------------------------------------------------------
 * Same case with strArr, but the return type is array of strings,
 * the array is of type array of strings, and value is of type string.
 * -------------------------------------------------------------------
 */
```

So now, if we try to use string functions on the returned value in `numArr`, Typescript will throw an error!

```typescript
console.log(numArr[0].split(''));//will give an error
console.log(strArr[0] - 5); //will give an error
```

To read more in-detail about typescript with examples, have a look [here](https://gist.github.com/sydrawat/00ac0cbf0bdf96c17294ae4aab14bb46).

### 🤯 Generics everywhere?!

We are working with generics all the time, one of the most prominent examples is an array.\
Consider the following example array:

```typescript
let dummyArr = [1, 2, 3, 4];
```

Here, the type is inferred, but we can explicitly define the type:

```typescript
let dummyArr: number[] = [1, 2, 3, 4];
```

Here "`number[]`" is just TypeScript notation for saying "this is an array of numbers". But `number[]` is just syntactical sugar. The actual type is `Array`. All arrays are of type `Array`. But since array type only makes sense if we also describe the type of values in the array, Array is actually a generic type.

```typescript
let dummyArr: Array<number> = [1, 2, 3, 4];
```

Here we have the angle brackets (`<>`) again! But this time NOT to create our own type, but instead to tell TypeScript which actual type should be used for the "generic type placeholder" (`T` in the previous section). We can use these in the previous example as well:

```typescript
const insertAtBeginning = <T>(array: T[], value: T) => {
  const newArray = [value, ...array];
  return newArray;
}

const dummyNumArr: Array<number> = [1, 2, 3];
const dummyStrArr: Array<string> = ['i', 'd'];

const numArr = insertAtBeginning<number>(dummyNumArr, 0); //[0, 1, 2, 3]
const strArr = insertAtBeginning<string>(dummyStrArr, 's'); //['s','i','d']
```

> So we can not just use the angle brackets to define a generic type but also to USE a generic type and explicitly set the placeholder type that should be used - sometimes this is required if TypeScript is not able to infer the (correct) type.

