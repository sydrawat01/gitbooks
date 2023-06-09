---
description: TypeScript is a "superset" of, or "extends" JavaScript.
---

# 🔮 TypeScript

## What is TypeScript?

TypeScript is a superset of JavaScript, so ideally it extends JavaScript. TypeScript adds **static typing** to JavaScript, because JavaScript itself is dynamically typed langauge.

### Why Static Typing?

Suppose we have the following function in JavaScript:

```javascript
const add = (a, b) => {
  return a + b;
}

const result = add(2, 5);
console.log(result); //7
```

Here, we see that the add function works as intended, since we are passing number to the `add` function. JavaScript knows the type of the values passed to the function, and hence performs addition. Now, suppose we send strings instead of numbers to the add function, like so:

```javascript
const add = (a, b) => {
  return a + b;
}

const result = add('2', '5');
console.log(result); //25
```

In the above case, JavaScript recognizes the numbers as strings, and concatenates them. So we get the result as 25 instead of 7. This is why & where we would find TypeScript useful, which is static typed.

## Installation & Usage

To install TypeScript locally in your project, use the following command:

```bash
yarn add typescript
```

To compile a specific file that is written in `.ts` format, you can use the following command:

```bash
npx tsc <file_name.ts>
```

This will compile the typescript file into a javascript file, irrespective of any errors / warnings in the typescript file. Let us try to compile the below typescript file:

{% code title="example.ts" %}
```typescript
const add = (a: number, b: number) => a + b;

console.log(add('2', '5');
```
{% endcode %}

When we compile the above file, we get a javascript file generated as output in the end, which is named `example.js` (with the `.js` extension). If there are any warnings, these will be displayed in the terminal, as well as your IDE.

```typescript
npx tsc example.ts
```

![Compiled .ts file, with warnings / errors.](<../../.gitbook/assets/Screenshot 2021-05-15 at 16.27.52.png>)

The resultant javascript file will be the same code, but in javascript syntax. Here is the output file from running the `tsc` command, even though there were errors:

{% code title="example.js" %}
```typescript
var addition = function (a, b) { return a + b; };
console.log(addition('2', '5'));
```
{% endcode %}

We can now attach this JS file to our `index.html` to render the content, i.e, log to the console the result of addition of two numbers, although we would need to correct the warning that is shown ny the typescript compiler here.
