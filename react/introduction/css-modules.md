---
description: Scoped CSS styles for React components.
---

# 💄 CSS Modules

## Adding a CSS Modules style-sheet

Note: this feature is available with react-scripts@2.0.0 and higher.

In case your project is using `react-scripts` version below v2.0.0 (_you can check this in your `package.json` file_), you should follow the below steps:

* If you have any unstaged changes, commit them.
* Next, run `npm run eject`
* This should give you two folders in your workspace, `scripts` and `config`.
* Inside the `config` folder, you should be able to see two `webpack` config files:
  * `webpack.config.dev.js`
  * `webpack.config.prod.js`
* In the `webpack.config.dev.js` file, look for `test: /\.css$/`. We will add CSS modules config here.
*   In the `use` array, inside the `options`object, of the above config object, we will add:

    ```jsx
    options: {
      importLoaders: 1,
      modules: true,
      localIdentName: '[name]__[local]__[hash:base64:5]',
      //...
    }
    ```
* This will enable the CSS modules features.
*   We need to add the same lines in `webpack.config.prod.js` file as well

    ```jsx
    options: {
      //...
      modules: true,
      localIdentName: '[name]__[local]__[hash:base64:5]',
      //...
    }
    ```
* re-run `yarn start`

The hash is used to generate unique CSS class names for the CSS modules.

### For react-scripts v2+

We can then use CSS Modules out of the box. No need to perform webpack changes as above.

To use CSS modules, we simply need to name our CSS files like: `<filename>.module.css` instead if `<filename>.css`.

This project supports [CSS Modules](https://github.com/css-modules/css-modules) alongside regular stylesheets using the `[name].module.css` file naming convention. CSS Modules allows the scoping of CSS by automatically creating a unique class name of the format `[filename]`**`[classname]`**`[hash]`.

**Tip**: Should you want to preprocess a stylesheet with Sass then make sure to follow the installation instructions and then change the stylesheet file extension as follows: `[name].module.scss` or `[name].module.sass`.

CSS Modules let you use the same CSS class name in different files without worrying about naming clashes. This is to say that the styles written in CSS modules are scoped to particular components.

Learn more about CSS Modules [here](https://css-tricks.com/css-modules-part-1-need/).

### Examples

{% code title="Button.module.css" %}
```css
.error {
  background-color: red;
}
```
{% endcode %}

{% code title="another-stylesheet.css" %}
```css
.error {
  color: red;
}
```
{% endcode %}

{% code title="Button.js" %}
```jsx
import React, {Component} from 'react';
import styles from './Button.module.css';// import css modules stylesheet as styles
import './another-stylesheet.css';// import regular stylesheet

class Button extends Component {
  render() {
    return <button className={styles.error}>Error Button</button>
  }
}
```
{% endcode %}

#### Result

No clashes from other .error class names

```markup
<!-- This button has red background but not red text -->

<button class="Button_error_ax7yz">Error button</button>
```

**This is an optional feature.** Regular  stylesheets and CSS files are fully supported. CSS Modules are turned on for files ending with the `.module.css` extension.

### Media Queries w/ CSS Modules

CSS Modules are basically scoped CSS Styles. So media queries written specific to classes will be added to the scope of the CSS class that is being used in the React component.

### More on CSS Modules

**CSS Modules** are a relatively new concept (you can dive super-deep into them [here](https://github.com/css-modules/css-modules)). With CSS modules, you can write normal CSS code and make sure, that it only applies to a given component.

It's not using magic for that, instead it'll simply **automatically generate unique CSS class names** for you. And by importing a JS object and assigning classes from there, you use these dynamically generated, unique names. So the imported JS object simply exposes some properties which hold the generated CSS class names as values.

#### **Example:**

**In `Post.css` File**

```css
.Post {
  color: red;
}
```

**In `Post` Component File**

```jsx
import classes from './Post.css;
const Post = () => (
  <div className={classes.Post}>...</div>
)
```

Here, `classes.Post` refers to an automatically generated `Post` property on the imported `classes` object. That property will in the end simply hold a value like `Post__Post__ah5_1` .

So your `.Post` class was automatically transformed to a different class (`Post__Post__ah5_1`) which is unique across the application. You also can't use it accidentally in other components because you don't know the generated string! You can only access it through the `classes` object. And if you import the CSS file (in the same way) in another component, the `classes` object there will hold a `Post` property which yields a **different** (!) CSS class name. Hence it's scoped to a given component.

By the way, if you somehow also want to define a global (i.e. un-transformed) CSS class in such a `.css` file, you can prefix the selector with `:global`.

**Example:**

```css
:global .Post {
  color: red;
  padding: 10px;
}
```

Now you can use `className="Post"` anywhere in your app and receive that styling.
