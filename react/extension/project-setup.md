---
description: >-
  This guide is to help understand what goes on under the hood of CRA
  application, and to grasp how the tools like Webpack and Babel work in tandem
  with React and Typescript.
---

# 📝 Project Setup

## Motivation

I had fun with `create-react-app,` with the no-worries setup it provides to dive right into the action of writing React components, but now it's time we get our hands dirty with the configuration that `create-react-app` does for us under the hood. This is basically to understand the basic concepts of how tools like [Webpack](https://webpack.js.org/) and [Babel](https://babeljs.io/) help us developers in writing better code, with the latest **ECMAScript** standards, while also ensuring that older browsers that do not support these latest standards, also work for the code that we write.

**P.S.** This is a really long article, stick till then end, it's going to be worth the effort! All the code for this guide is available on [Github](https://github.com/sydrawat/react-boilerplate).

## 1. Base setup

Let's start by creating a folder and initializing `git` and `yarn`, plus we'll also add two folders, `src` and `public`, which will house our code.

```bash
mkdir boilerplate; cd boilerplate
# initialize package.json
yarn init -y;
#  initialize git version control
git init
```

### `React` dependencies

Let's add the `react` and `react-dom` dependencies:

```bash
yarn add react react-dom
```

### Typescript & others

Let's add the Typescript and other dev dependencies: \[use the `-D` flag to save as dev dependency]

```bash
yarn add -D typescript @types/react @types/react-dom
```

With these dependencies added, we need to add a new file to the root directory of the app, called `tsconfig.json`. This file will contain all the configuration required for Typescript in our app.

{% code title="tsconfig.json" %}
```javascript
{
  "compilerOptions": {
    "target": "ES5" /* Specify ECMAScript target version: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017', 'ES2018', 'ES2019', 'ES2020', or 'ESNEXT'. */,
    "module": "ESNext" /* Specify module code generation: 'none', 'commonjs', 'amd', 'system', 'umd', 'es2015', 'es2020', or 'ESNext'. */,
    "moduleResolution": "node" /* Specify module resolution strategy: 'node' (Node.js) or 'classic' (TypeScript pre-1.6). */ /* Type declaration files to be included in compilation. */,
    "lib": [
      "DOM",
      "ESNext"
    ] /* Specify library files to be included in the compilation. */,
    "jsx": "react-jsx" /* Specify JSX code generation: 'preserve', 'react-native', 'react' or 'react-jsx'. */,
    "noEmit": true /* Do not emit outputs. */,
    "isolatedModules": true /* Transpile each file as a separate module (similar to 'ts.transpileModule'). */,
    "esModuleInterop": true /* Enables emit interoperability between CommonJS and ES Modules via creation of namespace objects for all imports. Implies 'allowSyntheticDefaultImports'. */,
    "strict": true /* Enable all strict type-checking options. */,
    "skipLibCheck": true /* Skip type checking of declaration files. */,
    "forceConsistentCasingInFileNames": true /* Disallow inconsistently-cased references to the same file. */,
    "resolveJsonModule": true
    // "allowJs": true /* Allow javascript files to be compiled. Useful when migrating JS to TS */,
    // "checkJs": true /* Report errors in .js files. Works in tandem with allowJs. */,
  },
  "include": ["src/**/*"]
}
```
{% endcode %}

### Babel & others

With the Typescript configuration in place, let's add babel and it's dev dependencies:

```bash
yarn add -D @babel/core @babel/preset-env @babel/preset-react @babel/preset-typescript
```

Create a new file in the root directory called .babelrc, which will contain the config options for babel.

{% code title=".babelrc" %}
```javascript
{
  "presets": [
    "@babel/preset-env",
    [
      "@babel/preset-react",
      {
        "runtime": "automatic"
      }
    ],
    "@babel/preset-typescript"
  ],
}
```
{% endcode %}

### Base Code

With this  basic setup in place, we can now write Typescript JSX, i.e, TSX in our app! But keep in mind, we can only write it, currently it cannot be compiled and displayed on the  browser, for which we will need Webpack!

{% tabs %}
{% tab title="Index" %}
{% code title="src/index.tsx" %}
```jsx
import ReactDOM from 'react-dom'
import App from './App'

ReactDOM.render(<App />, document.getElementById('root');
```
{% endcode %}
{% endtab %}

{% tab title="App" %}
{% code title="src/App.tsx" %}
```jsx
import { FC } from 'react'

const App: FC = () => {
  return (
    <>
      <h1>Hello</h1>
    </>
  );
}
```
{% endcode %}
{% endtab %}

{% tab title="HTML" %}
{% code title="public/index.html" %}
```markup
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <link rel="shortcut icon" type="image/svg" href="./favicon.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>React Boilerplate</title>
  </head>
  <body>
    <div id="root"></div>
  </body>
</html>
```
{% endcode %}
{% endtab %}
{% endtabs %}

## 2. Webpack

We need to inject our `<script>` into the `index.html` file. We do this by using webpack, which will **`transpile`** and **`bundle`** our React app into plain Javascript, and inject it into the HTML page in our app.

### Webpack & others

Let's add webpack and it's dev dependencies! Also, I'll be using a really cool webpack dashboard to see the status of my compilation and bundling,  it's called [`webpack-dashboard`](https://github.com/FormidableLabs/webpack-dashboard), and is open sourced by [`FormidableLabs`](https://github.com/FormidableLabs).

```bash
yarn add -D webpack webpack-cli webpack-dev-server html-webpack-plugin webpack-dashboard
```

With the above tools installed, we will need to create a new "script" in our `package.json` file, which points to the webpack configuration of our app. But before we start with the webpack configuration, we need to add another package, called `babel-loader`, which is required to transpile all the `.tsx` and `.jsx` files in out app!

```bash
yarn add -D babel-loader
```

### Configuration

Let's configure webpack. For this, we need to create a new file called `webpack.config.js` at the root level directory of our app.

{% code title="webpack.config.js" %}
```javascript
const path = require('path')
const HTMLWebpackPlugin = require('html-webpack-plugin')
const DashboardPlugin = require('webpack-dashboard/plugin')

module.exports = {
  entry: path.resolve(__dirname, './src/index.tsx'),
  mode: 'development',
  resolve: {
    extensions: ['.tsx', '.ts', '.js']
  },
  module: {
    rules: [
      {
        test: /\.(ts|js)x?$/,
        exclude: /node_modules/,
        use: [
          {
            loader: 'babel-loader'
          }
        ]
      }
    ]
  },
  output: {
    path: path.resolve(__dirname, './build'),
    filename: 'bundle.js'
  },
  plugins: [
    new HTMLWebpackPlugin({
      template: path.resolve(__dirname, './public/index.html')
    }),
    new DashboardPlugin()
  ],
  devServer: {
    port: 3000,
    open: true
  }
}
```
{% endcode %}

With the configuration done, we need to add the script to the `package.json` file:

```javascript
"script" : {
  "start": "webpack-dashboard -- webpack serve --config webpack.config.js"
}
```

We can finally test our app on the browser! Simply run `yarn start` in the terminal to see the app working on `localhost:3000`.

## 3. Prod and Dev Webpack Configs

It's usually a good idea to split out webpack configuration into different  files. We usually 4 webpack config files:&#x20;

* **webpack.dev.js** : This contains the config for the webpack dev server
* **webpack.prod.js**: This contains the config for building our app for production
* **webpack.common.js**: This contains the configs that are common to both dev & prod
* **webpack.config.js**: This '**merges**' both _dev_ **OR** _prod_ config file **AND** the _common_ config file

Let us setup this structure for our app! We'll create a `webpack/` folder in the root directory of our app, and create 3 files in it, viz., `webpack.common.js`, `webpack.dev.js` and `webpack.prod.js`. We would have to change the content of webpack.config.js (which is in the root directory, and not the webpack folder) as well. But first, we need to install a package that merges two webpack configuration files.

```bash
yarn add -D webpack-merge
```

With this package installed, we are ready to configure our webpack configuration files!

{% tabs %}
{% tab title="common" %}
{% code title="webpack/webpack.common.js" %}
```javascript
const path = require('path')
const HTMLWebpackPlugin = require('html-webpack-plugin')
const DashboardPlugin = require('webpack-dashboard/plugin')

module.exports = {
  entry: path.resolve(__dirname, './src/index.tsx'),
  resolve: {
    extensions: ['.tsx', '.ts', '.js']
  },
  module: {
    rules: [
      {
        test: /\.(ts|js)x?$/,
        exclude: /node_modules/,
        use: [
          {
            loader: 'babel-loader'
          }
        ]
      }
    ]
  },
  output: {
    path: path.resolve(__dirname, './build'),
    filename: 'bundle.js'
  },
  plugins: [
    new HTMLWebpackPlugin({
      template: path.resolve(__dirname, './public/index.html')
    }),
    new DashboardPlugin()
  ]
}
```
{% endcode %}
{% endtab %}

{% tab title="dev" %}
{% code title="webpack/webpack.dev.js" %}
```javascript
module.exports = {
  mode: 'development',
  devtool: 'cheap-module-source-map',
  devServer: {
    port: 3000,
    open: true
  }
}
```
{% endcode %}
{% endtab %}

{% tab title="prod" %}
{% code title="webpack/webpack.prod.js" %}
```javascript
module.exports = {
  mode: 'production',
  devtool: 'source-map',
}
```
{% endcode %}
{% endtab %}

{% tab title="config" %}
{% code title="./webpack.config.js" %}
```javascript
const { merge } = require('webpack-merge')
const commonConfig = require('./webpack/webpack.common')

module.exports = ({env}) => {
  const envConfig = require(`./webpack/webpack.${env}`)
  const config = merge(commonConfig, envConfig)
  return config
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

## 4. Adding styles

Since there are many options out there to  style a React app, we'll be focusing on  really the basic tools for styling an app, i.e, CSS, CSS-modules and SASS. These are the most widely used styling tools used and are relatively easy to set up with webpack! We will add different rules for development and production modes.

### Installing dev dependencies

Before we start adding rules to webpack config files for styling, we need to install a few loaders that can handle CSS and SASS:

```bash
yarn add -D style-loader css-loader sass-loader node-sass mini-css-extract-plugin
```

### Webpack Configs for Styling

{% tabs %}
{% tab title="dev" %}
{% code title="webpack/webpack.dev.js" %}
```javascript
module.exports = {
  //...
  module: {
    rules: [
      {
        test: /\.(scss|css)$/,
        include: /\.module\.css$/,
        use: [
          'style-loader',
          {
            loader: 'css-loader',
            options: {
              importLoader: 1,
              modules: {
                localIndentName: '[name]_[local]_[hash:base64]',
                auto: true
              },
            }
          },
          'sass-loader
        ]
      },
      {
        test: /\.(scss|css)$/,
        exclude: /\.module\.css$/,
        use: ['style-loader', 'css-loader', 'sass-loader']
      }
    ]
  },
  //...
}
```
{% endcode %}
{% endtab %}

{% tab title="prod" %}
{% code title="webpack/webpack.prod.js" %}
```javascript
const MiniCssExtractPlugin = require('mini-css-extract-plugin')

module.exports = {
  //...
  module: {
    rules: [
      {
        test: /\.(scss|css)$/,
        include: /\.module\.css$/,
        use: [
          MiniCssExtractPlugin.loader,
          {
            loader: 'css-loader',
            options: {
              importLoader: 1,
              modules: {
                localIndentName: '[name]_[local]_[hash:base64]',
                auto: true
              },
            }
          },
          'sass-loader
        ]
      },
      {
        test: /\.(scss|css)$/,
        exclude: /\.module\.css$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader', 'sass-loader']
      }
    ]
  },
  plugins: [new MiniCssExtractPlugin()]
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Add `typings.d.ts`

With these configurations in place, we need to do one final thing, add a new file in the `src/` folder called `typings.d.ts` , which will have the modules declaration in it. Let's add some basic modules that we will declare in this file, and then move on to add configurations for using images and other stuff in our app with webpack!

{% code title="src/typings.d.ts" %}
```javascript
declare module '*.module.css'
declare module '*.scss'
declare module '*.css'
declare module '*.svg'
```
{% endcode %}

## 5. Images and SVGs

All apps require images and SVGs in their code. To bundle such files in our app with webpack, we do not need to install any extra dependencies! Prior to webpack 5 & 4, we had to install the '_file-loader_' dependency with other things to use images and SVGs in our app. Now, we need to just use the `type` property set to **`asset/resource`** or **`asset/inline`** to use  images or SVGs in our app respectively. Not only images and SVGs, we can also use other file with file-extenstions like **.gif, .jpg, .jpeg, .woff, .eot**, etc.

We will declate these configs in our common webpack config file:

{% code title="webpack/webpack.common.js" %}
```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.(?:ico|png|jpg|jpeg|gif)$/i,
        type: 'asset/resource',
      },
      {
        test: /\.(woff(2)?|eot|ttf|ots|svg|)$/,
        type: 'asset/inline',
      },
    ]
  }
}
```
{% endcode %}

## 6. Misc Webpack Dependencies

Here are a few common dependencies that are typically used in every production ready application. They are not absolutely necessary to install, but it's recommended that you do, since they are pretty useful!

### React Refresh Webpack Plugin

This is a plugin that adds hot reloading to our app.

```bash
yarn add -D @pmmmwh/react-refresh-webpack-plugin react-refresh
```

{% code title="webpack/webpack.dev.js" %}
```javascript
const ReactRefreshWebpackPlugin = require('@pmmmwh/react-refresh-webpack-plugin')

module.exports = {
  //...
  devServer: {
    //...
    hot: true
  },
  plugins: [new ReactRefreshWebpackPlugin()]
}
```
{% endcode %}

### Dotenv Webpack Plugin

This plugin helps us use environment variables throughout our app, and utilizes the functionality of `webpack.DefinePlugin` to declare global constants which can be declared at compile time, and therefore helps us hide important information for the source code and the end-user.

```bash
yarn add -D dotenv-webpack
```

{% code title="webpack/webpack.common.js" %}
```javascript
//...
const Dotenv = require('dotenv-webpack')

module.exports = {
  //...
  plugins: [new Dontenv()]
}
```
{% endcode %}

With this setup in place, we can define a `.env` file in our app's root directory and declare global constants that we can define during runtime.

### Update `package.json`

We need to add the scripts to the `package.json` file from where we can run the webpack server for production or development. We'll add the `rimraf` package as well, to delete all previous build folders, so as to start a clean build every time we build our app!

```bash
yarn add -D rimraf
```

{% code title="./package.json" %}
```javascript
{
  //...
  "scripts": {
    "start": "webpack-dashboard -- webpack serve --config webpack.config.js --env env=dev",
    "prebuild": "rimraf build",
    "build": "webpack --config webpack.config.js --env env=prod"
  }
  //...
}
```
{% endcode %}

## 7. ESLint

The ESLint package helps use developers write code that is as accurate as possible by pointing our errors as we write our code. Pretty useful feature to have linting in our code to highlight errors and warnings that indicate problems with our code.

A prerequisite for this dependency is that we need to have the ESLint extension installed in VSCode. This extension can be found [here](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint).

```bash
yarn add -D eslint eslint-plugin-react eslint-plugin-react-hooks
yarn add -D @typescript-eslint/parser @typescript-eslint/eslint-plugin
yarn add -D eslint-pligin-import eslint-plugon-jsx-a11y
```

Now, we have to define the configurations for ESLint in a new file at the app's root directory, called '`.eslintrc.js`'.

{% code title=".eslintrc.js" %}
```javascript
module.exports = {
  parser: '@typescript-eslint/parser',
  parserOptions: {
    ecmaVersion: 2020,
    sourceType: 'module',
  },
  settings: {
    react: {
      version: 'detect',
    },
  },
  extends: [
    'plugin:react/recommended',
    'plugin:react-hooks/recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:import/errors',
    'plugin:import/warnings',
    'plugin:import/typescript',
    'plugin:jsx-a11y/recommended',
  ],
  rules: {
    'no-unused-vars': 'off',
    '@typescript-eslint/no-unused-vars': ['error'],
    '@typescript-eslint/no-var-requires': 'off',
    'react/prop-types': 'off',
    'react/jsx-uses-react': 'off',
    'react/react-in-jsx-scope': 'off',
    '@typescript-eslint/explicit-module-boundary-types': 'off',
  },
}
```
{% endcode %}

Let's take it to the next level by adding a script to run eslint on our files:

{% code title="package.json" %}
```javascript
{
  "scripts": {
    "lint": "eslint --fix \"./src/**/*.{js,jsx,ts,tsx,json}\""
  }
}
```
{% endcode %}

## 8. Prettier

This is basically a style guide for our code, and helps maintain a standard for writing code for an application. Again, a prerequisite for this to work is we have the Prettier extensions installed on VSCode. This extension can be found  [here](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode).

```bash
yarn add -D prettier eslint-config-prettier eslint-plugin-prettier
```

To configure prettier, a '`.prettierrc.js`' file is required at the app's root directory.

{% code title=".prettierrc.js" %}
```javascript
module.exports = {
  semi: false,
  trailingComma: "es5",
  singleQuote: true,
  jsxSingleQuote: false,
  printWidth: 80,
  tabWidth: 2,
  endOfLine: "auto",
}
```
{% endcode %}

We need to modify the `.eslintrc.js` file a little bit as well:

{% code title=".eslintrc.js" %}
```javascript
module.exports = {
  //...
  extends: [
    //...
    'prettier',
    'prettier/@typescript-eslint',
    'plugin:prettier/recommended',
  ],
  //...
}
```
{% endcode %}

Time to take it to the next level, by adding a script to run prettier on all our files:

{% code title="package.json" %}
```javascript
{
  "script": {
    "format": "prettier --write \"./src/**/*.{js,jsx,ts,tsx,json,css,scss,md}\""
  }
}
```
{% endcode %}

We can also turn on the '_Format on Save'_ option in VSCode to `true`, to format our files when we save them.

## 9. Husky & `lint-staged`

With `Husky` and `lint-staged` packages, we prevent linting and formatting errors to be committed to a common work repository for our app. This help maintain a code standard, and thus prevents accidental errors in our code or formatting of our code to be committed into the repository.

```bash
yarn add -D husky@4 lint-staged
```

### Define `lint-staged` configs

{% code title="package.json" %}
```javascript
{
  //...
  "lint-staged": {
    "src/**/*.{js,jsx,ts,tsx,json}": [
      "eslint --fix"
    ],
    "src/**/*.{js,jsx,ts,tsx,json,css,scss,md}": [
      "prettier --write"
    ]
  }
}
```
{% endcode %}

### Define Husky configs

We define the husky config that tells `husky` to run the `lint-staged` configurations just before the code is being committed.

{% code title="package.json" %}
```javascript
  {
    //...
    "husky": {
      "hooks": {
        "pre-commit": "lint-staged"
      }    
    }
  }
```
{% endcode %}

## 10. Nice-to-have(s)

Below are some other configurations for our React + Typescript app that are not necessarily required, but are good to have.

### Babel & Runtime

These plugins let us us the `async-await` features in our application.

```bash
yarn add -D @babel/runtime @babel/plugin-transform-runtime
```

We'll update the `.babelrc` file to incorporate the plugins.

{% code title=".babelrc" %}
```javascript
{
  //...
  "plugins": [
    [
      "@babel/plugin-transform-runtime",
      {
        "regenerator": true
      }
    ]
  ]
}
```
{% endcode %}

### Copy Webpack Plugin

This plugin helps us copy static assets from a source to a destination, typically used to copy the contents of the source folder into the build folder, when the build command is run.

```bash
yarn add -D copy-webpack-plugin
```

{% code title="webpack/webpack.common.js" %}
```javascript
//...
const CopyWebpackPlugin = require('copy-webpack-plugin')

module.exports = {
  //...
  plugins: [
    new CopyWebpackPlugin({
      patterns: [{from : './src/**/*', to: './build' }]
    })
  ]
}
```
{% endcode %}

### Bundle Analyzer Plugin

This plugin runs when we build our app for production. It loads a webpage that displays all the bundled files and their sizes. Pretty useful feature to use in an app that is being deployed.

```bash
yarn add -D webpack-bundle-analyzer
```

{% code title="webpack/webpack.prod.js" %}
```javascript
//...
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin

module.exports = {
  //...
  plugins: [new BundleAnalyzerPlugin()]
}
```
{% endcode %}
