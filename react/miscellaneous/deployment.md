---
description: Moving our app from local development environment out into the web.
---

# 🚀 Deployment

## Deployment steps

![Basic steps involved in deploying a web app.](<../.gitbook/assets/Screenshot 2021-05-20 at 12.09.35.png>)

## Lazy Loading

Lazy loading means we load only chunks or part of our code only when that code is needed. In the end, our SPA React application is JavaScript code that is bundled and downloaded by the end user. The use has to wait until the files are downloaded to their browser and only after they have been downloaded successfully, can the user see the UI and work with the app.

So, we want the initial code bundle that the user's browser downloads, to be as small and minimal as possible, and certain parts of our code is downloaded only when we visit that part of the application.

`Lazy Loading` is the concept where we break our application into smaller chunks and which are then only downloaded when we need them. Lazy loading is easy to implement with routes since it is easy to break or split up code based on routes, so that the code for a specific route is only downloaded when we visit that particular route.

### `React.lazy()`

We can split our code with the help of an inbuilt function that React provides, called `lazy()` and is used with `React.lazy()` on the part of our code that we want to split from the rest of the application for lazy loading.

The `lazy()` method requires an inline anon function, which resolves to a dynamic import. So we can call `import()` as a function, and to this function we pass the path to the part of the code that we want to be `lazy loaded`.

```jsx
import React from 'react';
//import MyComponent from './pages/MyComponent';
//...
const MyComponent = React.lazy(() => import('./path/MyComponent'));
//...
//...
<Route path='/products/:productID'>
  <MyComponent />
</Route>
```

The anon function in `React.lazy()` is executed by React when the `MyComponent` is required. This creates a new code `chunk` which is only downloaded when it is needed, and not when the app loads for the first time.

### `Suspense`

There is a tiny problem with this code snippet, because downloading this `chunk` of code that is needed might take some milliseconds to download, and until this `chunk` is downloaded, React is unable to continue. Hence, we need a `fallback UI` which would be shown until the time this `chunk` is being downloaded. For this, we need to use the `Suspense` component which is provided by React.

We need to wrap our component that is `lazy loaded` with this `Suspense` component. On this `Suspense` component, we need to add a `fallback` prop. This prop requires JSX code as value to it, which in the end is shown as a fallback when the lazy-loaded component is still being downloaded. This is barely seen since downloading the component is fairly fast.

```jsx
import React, {Suspense} from 'react';
import LoadingSpinner from './components/UI/LoadingSpinner';
//...
//...
const MyComponent = React.lazy(() => import('./path/MyComponent'));
//...
const fallbackSpinner = (
  <div className="centered">
    <LoadingSpinner />
  </div>
);
return (
  <Layout>
    <Suspense fallback={fallbackSpinner}>
      //...
      //...
      <Route path=''>
    <MyComponent />
      </Route>
    </Suspense>
  </Layout
)
```

We can add lazy loading to all the routes in our application this way. This helps in making the code bundle smaller and our application loading faster in the browser. We can check the network tab to see which files are loaded using `lazy loading`(tiny turtle icon in front of the `chunk` filename):

## Build code for production

The `build` command comes built-in with `create-react-app`. Inside the `package.json` file, we can see a `scripts` section, which has all the scripts that come bundled with `create-react-app`.

{% code title="package.json" %}
```javascript
"scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
```
{% endcode %}

This `build` script transforms and "minifies" our code, optimize and shrink it as much as possible. Important thing here, running the `build` script will not start a server. Use the following command to run the `build` script:

```bash
# If using yarn:
yarn build
# If using npm:
npm run build
```

After running this command successfully, it shows us a list of all the files it has bundled and "minified" for our app in the `build/` folder.

The `build/` folder contains all the code that we need to deploy our code to a server of a hosting service. Inside this folder, we have the `static/` folder, which has the optimized `css/` and `js/` code. This is all browser ready code.

## Upload production code to server

The React SPA is a `static website`, i.e, only HTML, CSS and Javascript. So when we need to deploy our React application, we need a `static site host`.

I mainly use [Netlify](https://app.netlify.com/) or [Firebase](https://firebase.google.com/) for serving my static sites. We can also use AWS for our hosting purposes.

### Hosting on Firebase:

* **Install Firebase CLI**

```bash
npm install -g firebase-tools
```

* **Initialize your project** Open a terminal window and navigate to or create a root directory for your web app
  *   **Sign-in to Google**

      ```bash
      firebase login
      ```
  *   **Initiate your project** Run this command from your app's root directory:

      ```bash
      firebase init
      ```
* **Deploy to Firebase Hosting**
  *   **When you're ready, deploy your web app:** Put your static files (e.g. HTML, CSS, JS) in your app’s deploy directory (the default is 'public'). Then, run this command from your app’s root directory:

      ```bash
      firebase deploy
      ```
* **Disable Firebase Hosting**
  *   To take your web app down, run the following command in the app's root directory:

      ```bash
      firebase hosting:disable
      ```

### Server-side Routing vs Client-side Routing

Typically, when a user enters a URL in the browser, the `client` sends a `request` to the `server` , where our production ready React code is present, which then `reponds` to the request and returns the HTML, CSS and JS React code with the specific `route` that we configured our app with, to be shown in the client side.

But, in case the user enters a specific `route` to the server, say `/some-route`, but the routing logic is not present in our server, but on the client side. So we need to configure our hosting server to ignore such requests and to redirect our app to the route defined on the client side. This can be done by checking out the documentation for our hosting server.

This is rather simple in Firebase, since it directly asks us to re-direct all our URLs to the `index.html`:

## Deployment

`npm run build` creates a `build` directory with a production build of your app. Set up your favorite HTTP server so that a visitor to your site is served `index.html`, and requests to static paths like `/static/js/main.<hash>.js` are served with the contents of the `/static/js/main.<hash>.js` file.

### Static Server

For environments using [Node](https://nodejs.org/), the easiest way to handle this would be to install [serve](https://github.com/zeit/serve) and let it handle the rest:

```bash
npm install -g serve
serve -s build
```

The last command shown above will serve your static site on the port **5000**. Like many of [serve](https://github.com/zeit/serve)’s internal settings, the port can be adjusted using the `-p` or `--port` flags.

Run this command to get a full list of the options available:

```bash
serve -h
```

### Other Solutions

You don’t necessarily need a static server in order to run a Create React App project in production. It works just as fine integrated into an existing dynamic one.

Here’s a programmatic example using [Node](https://nodejs.org/) and [Express](http://expressjs.com/):

```jsx
const express = require('express');
const path = require('path');
const app = express();

app.use(express.static(path.join(__dirname, 'build')));

app.get('/', function (req, res) {
  res.sendFile(path.join(__dirname, 'build', 'index.html'));
});

app.listen(9000);
```

The choice of your server software isn’t important either. Since Create React App is completely platform-agnostic, there’s no need to explicitly use Node.

The `build` folder with static assets is the only output produced by Create React App.

However this is not quite enough if you use client-side routing. Read the next section if you want to support URLs like `/todos/42` in your single-page app.

### Serving Apps with Client-Side Routing

If you use routers that use the HTML5 [`pushState` history API](https://developer.mozilla.org/en-US/docs/Web/API/History\_API#Adding\_and\_modifying\_history\_entries) under the hood (for example, [React Router](https://github.com/ReactTraining/react-router) with `browserHistory`), many static file servers will fail. For example, if you used React Router with a route for `/todos/42`, the development server will respond to `localhost:3000/todos/42` properly, but an Express serving a production build as above will not.

This is because when there is a fresh page load for a `/todos/42`, the server looks for the file `build/todos/42` and does not find it. The server needs to be configured to respond to a request to `/todos/42` by serving `index.html`. For example, we can amend our Express example above to serve `index.html` for any unknown paths:

```jsx
 app.use(express.static(path.join(__dirname, 'build')));

-app.get('/', function (req, res) {
+app.get('/*', function (req, res) {
   res.sendFile(path.join(__dirname, 'build', 'index.html'));
 });
```

If you’re using [Apache HTTP Server](https://httpd.apache.org/), you need to create a `.htaccess` file in the `public` folder that looks like this:

```
    Options -MultiViews
    RewriteEngine On
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^ index.html [QSA,L]
```

It will get copied to the `build` folder when you run `npm run build`.

If you’re using [Apache Tomcat](http://tomcat.apache.org/), you need to follow [this Stack Overflow answer](https://stackoverflow.com/a/41249464/4878474).

Now requests to `/todos/42` will be handled correctly both in development and in production.

On a production build, and in a browser that supports [service workers](https://developers.google.com/web/fundamentals/getting-started/primers/service-workers), the service worker will automatically handle all navigation requests, like for`/todos/42`, by serving the cached copy of your `index.html`. This service worker navigation routing can be configured or disabled by `eject`-ing and then modifying the [`navigateFallback`](https://github.com/GoogleChrome/sw-precache#navigatefallback-string) and [`navigateFallbackWhitelist`](https://github.com/GoogleChrome/sw-precache#navigatefallbackwhitelist-arrayregexp) options of the `SWPreachePlugin` configuration.

When users install your app to the home screen of their device the default configuration will make a shortcut to `/index.html`. This may not work for client-side routers which expect the app to be served from `/`. Edit the web app manifest at `public/manifest.json` and change `start_url` to match the required URL scheme, for example:

```javascript
  "start_url": ".",
```

#### Building for Relative Paths

By default, Create React App produces a build assuming your app is hosted at the server root. To override this, specify the `homepage` in your `package.json`, for example:

```javascript
"homepage": "http://mywebsite.com/relativepath",
```

This will let Create React App correctly infer the root path to use in the generated HTML file.

**Note**: If you are using `react-router@^4`, you can root `<Link>`s using the `basename` prop on any `<Router>`. More information [here](https://reacttraining.com/react-router/web/api/BrowserRouter/basename-string). For example:

```jsx
<BrowserRouter basename="/calendar"/>
<Link to="/today"/> // renders <a href="/calendar/today">
```

#### Serving the Same Build from Different Paths

> Note: this feature is available with react-scripts@0.9.0 and higher.

If you are not using the HTML5 `pushState` history API or not using client-side routing at all, it is unnecessary to specify the URL from which your app will be served. Instead, you can put this in your `package.json`:

```javascript
  "homepage": ".",
```

This will make sure that all the asset paths are relative to `index.html`. You will then be able to move your app from `http://mywebsite.com` to `http://mywebsite.com/relativepath` or even `http://mywebsite.com/relative/path` without having to rebuild it.

#### [Azure](https://azure.microsoft.com/)

See [this](https://medium.com/@to\_pe/deploying-create-react-app-on-microsoft-azure-c0f6686a4321) blog post on how to deploy your React app to Microsoft Azure.

See [this](https://medium.com/@strid/host-create-react-app-on-azure-986bc40d5bf2#.pycfnafbg) blog post or [this](https://github.com/ulrikaugustsson/azure-appservice-static) repository for a way to use automatic deployment to Azure App Service.

#### [Firebase](https://firebase.google.com/)

Install the Firebase CLI if you haven’t already by running `npm install -g firebase-tools`. Sign up for a [Firebase account](https://console.firebase.google.com/) and create a new project. Run `firebase login` and login with your previous created Firebase account.

Then run the `firebase init` command from your project’s root. You need to choose the **Hosting: Configure and deploy Firebase Hosting sites** and choose the Firebase project you created in the previous step. You will need to agree with `database.rules.json` being created, choose `build` as the public directory, and also agree to **Configure as a single-page app** by replying with `y`.

```bash
    === Project Setup

    First, let's associate this project directory with a Firebase project.
    You can create multiple project aliases by running firebase use --add,
    but for now we'll just set up a default project.

    ? What Firebase project do you want to associate as default? Example app (example-app-fd690)

    === Database Setup

    Firebase Realtime Database Rules allow you to define how your data should be
    structured and when your data can be read from and written to.

    ? What file should be used for Database Rules? database.rules.json
    ✔  Database Rules for example-app-fd690 have been downloaded to database.rules.json.
    Future modifications to database.rules.json will update Database Rules when you run
    firebase deploy.

    === Hosting Setup

    Your public directory is the folder (relative to your project directory) that
    will contain Hosting assets to uploaded with firebase deploy. If you
    have a build process for your assets, use your build's output directory.

    ? What do you want to use as your public directory? build
    ? Configure as a single-page app (rewrite all urls to /index.html)? Yes
    ✔  Wrote build/index.html

    i  Writing configuration info to firebase.json...
    i  Writing project information to .firebaserc...

    ✔  Firebase initialization complete!
```

IMPORTANT: you need to set proper HTTP caching headers for `service-worker.js` file in `firebase.json` file or you will not be able to see changes after first deployment ([issue #2440](https://github.com/facebookincubator/create-react-app/issues/2440)). It should be added inside `"hosting"` key like next:

```javascript
{
  "hosting": {
    ...
    "headers": [
      {"source": "/service-worker.js", "headers": [{"key": "Cache-Control", "value": "no-cache"}]}
    ]
    ...
```

Now, after you create a production build with `npm run build`, you can deploy it by running `firebase deploy`.

```bash
    === Deploying to 'example-app-fd690'...

    i  deploying database, hosting
    ✔  database: rules ready to deploy.
    i  hosting: preparing build directory for upload...
    Uploading: [==============================          ] 75%✔  hosting: build folder uploaded successfully
    ✔  hosting: 8 files uploaded successfully
    i  starting release process (may take several minutes)...

    ✔  Deploy complete!

    Project Console: https://console.firebase.google.com/project/example-app-fd690/overview
    Hosting URL: https://example-app-fd690.firebaseapp.com
```

For more information see [Add Firebase to your JavaScript Project](https://firebase.google.com/docs/web/setup).

#### [GitHub Pages](https://pages.github.com/)

> Note: this feature is available with react-scripts@0.2.0 and higher.

#### Step 1: Add `homepage` to `package.json`

**The step below is important!If you skip it, your app will not deploy correctly.**

Open your `package.json` and add a `homepage` field for your project:

```javascript
  "homepage": "https://myusername.github.io/my-app",
```

or for a GitHub user page:

```javascript
  "homepage": "https://myusername.github.io",
```

Create React App uses the `homepage` field to determine the root URL in the built HTML file.

#### Step 2: Install `gh-pages` and add `deploy` to `scripts` in `package.json`

Now, whenever you run `npm run build`, you will see a cheat sheet with instructions on how to deploy to GitHub Pages.

To publish it at [https://myusername.github.io/my-app](https://myusername.github.io/my-app), run:

```bash
npm install --save gh-pages
```

Alternatively you may use `yarn`:

```bash
yarn add gh-pages
```

Add the following scripts in your `package.json`:

```javascript
  "scripts": {
+   "predeploy": "npm run build",
+   "deploy": "gh-pages -d build",
    "start": "react-scripts start",
    "build": "react-scripts build",
```

The `predeploy` script will run automatically before `deploy` is run.

If you are deploying to a GitHub user page instead of a project page you'll need to make two additional modifications:

1. First, change your repository's source branch to be any branch other than **master**.
2. Additionally, tweak your `package.json` scripts to push deployments to **master**:

```javascript
  "scripts": {
    "predeploy": "npm run build",
-   "deploy": "gh-pages -d build",
+   "deploy": "gh-pages -b master -d build",
```

#### Step 3: Deploy the site by running `npm run deploy`

Then run:

```bash
npm run deploy
```

#### Step 4: Ensure your project’s settings use `gh-pages`

Finally, make sure **GitHub Pages** option in your GitHub project settings is set to use the `gh-pages` branch:

[https://camo.githubusercontent.com/ae23eb7faebc46a07ba12c50e15edda89b3c2153387b8eff6c3aea94eaf6b31c/687474703a2f2f692e696d6775722e636f6d2f48556a4572396c2e706e67](https://camo.githubusercontent.com/ae23eb7faebc46a07ba12c50e15edda89b3c2153387b8eff6c3aea94eaf6b31c/687474703a2f2f692e696d6775722e636f6d2f48556a4572396c2e706e67)

#### Step 5: Optionally, configure the domain

You can configure a custom domain with GitHub Pages by adding a `CNAME` file to the `public/` folder.

#### Notes on client-side routing

GitHub Pages doesn’t support routers that use the HTML5 `pushState` history API under the hood (for example, React Router using `browserHistory`). This is because when there is a fresh page load for a URL like `http://user.github.io/todomvc/todos/42`, where `/todos/42` is a front-end route, the GitHub Pages server returns 404 because it knows nothing of `/todos/42`. If you want to add a router to a project hosted on GitHub Pages, here are a couple of solutions:

* You could switch from using HTML5 history API to routing with hashes. If you use React Router, you can switch to `hashHistory` for this effect, but the URL will be longer and more verbose (for example, `http://user.github.io/todomvc/#/todos/42?_k=yknaj`). [Read more](https://reacttraining.com/react-router/web/api/Router) about different history implementations in React Router.
* Alternatively, you can use a trick to teach GitHub Pages to handle 404 by redirecting to your `index.html` page with a special redirect parameter. You would need to add a `404.html` file with the redirection code to the `build` folder before deploying your project, and you’ll need to add code handling the redirect parameter to `index.html`. You can find a detailed explanation of this technique [in this guide](https://github.com/rafrex/spa-github-pages).

#### Troubleshooting

#### "_/dev/tty: No such a device or address_"

If, when deploying, you get `/dev/tty: No such a device or address` or a similar error, try the following:

1. Create a new [Personal Access Token](https://github.com/settings/tokens)
2.  Set the remote repository URL:

    ```bash
     git remote set-url origin https://<user>:<token>@github.com/<user>/<repo>
    ```
3.  Try:

    ```bash
     npm run deploy again
    ```

#### [Heroku](https://www.heroku.com/)

Use the [Heroku Buildpack for Create React App](https://github.com/mars/create-react-app-buildpack). You can find instructions in [Deploying React with Zero Configuration](https://blog.heroku.com/deploying-react-with-zero-configuration).

#### Resolving Heroku Deployment Errors

Sometimes `npm run build` works locally but fails during deploy via Heroku. Following are the most common cases.

#### "Module not found: Error: Cannot resolve 'file' or 'directory'"

If you get something like this:

```
remote: Failed to create a production build. Reason:
remote: Module not found: Error: Cannot resolve 'file' or 'directory'
MyDirectory in /tmp/build_1234/src
```

It means you need to ensure that the letter-case of the file or directory you `import` matches the one you see on your filesystem or on GitHub.

This is important because Linux (the operating system used by Heroku) is case sensitive. So `MyDirectory` and `mydirectory` are two distinct directories and thus, even though the project builds locally, the difference in case breaks the `import` statements on Heroku remotes.

#### "_Could not find a required file._"

If you exclude or ignore necessary files from the package you will see a error similar this one:

```
remote: Could not find a required file.
remote:   Name: `index.html`
remote:   Searched in: /tmp/build_a2875fc163b209225122d68916f1d4df/public
remote:
remote: npm ERR! Linux 3.13.0-105-generic
remote: npm ERR! argv "/tmp/build_a2875fc163b209225122d68916f1d4df/.heroku/node/bin/node" "/tmp/build_a2875fc163b209225122d68916f1d4df/.heroku/node/bin/npm" "run" "build"
```

In this case, ensure that the file is there with the proper letter-case and that’s not ignored on your local `.gitignore` or `~/.gitignore_global`.

#### [Netlify](https://www.netlify.com/)

**To do a manual deploy to Netlify’s CDN:**

```bash
npm install netlify-cli -g
netlify deploy
```

Choose `build` as the path to deploy.

**To setup continuous delivery:**

With this setup Netlify will build and deploy when you push to git or open a pull request:

1. [Start a new netlify project](https://app.netlify.com/signup)
2. Pick your Git hosting service and select your repository
3. Set `yarn build` as the build command and `build` as the publish directory
4. Click `Deploy site`

**Support for client-side routing:**

To support `pushState`, make sure to create a `public/_redirects` file with the following rewrite rules:

```
/*  /index.html  200
```

When you build the project, Create React App will place the `public` folder contents into the build output.

#### [Now](https://zeit.co/now)

Now offers a zero-configuration single-command deployment. You can use `now` to deploy your app for free.

1. Install the `now` command-line tool either via the recommended [desktop tool](https://zeit.co/download) or via node with `npm install -g now`.
2. Build your app by running `npm run build`.
3. Move into the build directory by running `cd build`.
4.  Run `now --name your-project-name` from within the build directory. You will see a **now.sh** URL in your output like this:

    ```
     > Ready! https://your-project-name-tpspyhtdtk.now.sh (copied to clipboard)
    ```

    Paste that URL into your browser when the build is complete, and you will see your deployed app.

Details are available in [this article.](https://zeit.co/blog/unlimited-static)

#### [S3](https://aws.amazon.com/s3) and [CloudFront](https://aws.amazon.com/cloudfront/)

See this [blog post](https://medium.com/@omgwtfmarc/deploying-create-react-app-to-s3-or-cloudfront-48dae4ce0af) on how to deploy your React app to Amazon Web Services S3 and CloudFront.

#### [Surge](https://surge.sh/)

Install the Surge CLI if you haven’t already by running `npm install -g surge`. Run the `surge` command and log in you or create a new account.

When asked about the project path, make sure to specify the `build` folder, for example:

```bash
project path: /path/to/project/build
```

Note that in order to support routers that use HTML5 `pushState` API, you may want to rename the `index.html` in your build folder to `200.html` before deploying to Surge. This [ensures that every URL falls back to that file](https://surge.sh/help/adding-a-200-page-for-client-side-routing).

### Advanced Configuration

You can adjust various development and production settings by setting environment variables in your shell or with `.env`.

| Variable             | Development | Production | Usage                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| -------------------- | ----------- | ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| BROWSER              | ✅           | ❌          | By default, Create React App will open the default system browser, favoring Chrome on macOS. Specify a browser to override this behavior, or set it to `none` to disable it completely. If you need to customize the way the browser is launched, you can specify a node script instead. Any arguments passed to `npm start` will also be passed to this script, and the URL where your app is served will be the last argument. Your script's file name must have the `.js` extension.                                                                                                                                                                                            |
| HOST                 | ✅           | ❌          | By default, the development web server binds to `localhost`. You may use this variable to specify a different host.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| PORT                 | ✅           | ❌          | By default, the development web server will attempt to listen on port 3000 or prompt you to attempt the next available port. You may use this variable to specify a different port.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| HTTPS                | ✅           | ❌          | When set to `true`, Create React App will run the development server in `https` mode.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| PUBLIC\_URL          | ❌           | ✅          | <p>Create React App assumes your application is hosted at the serving web server's root or a subpath as specified in <code>package.json</code></p><p><code>(homepage)</code>. Normally, Create React App ignores the hostname. You may use this variable to force assets to be referenced verbatim to the url you provide (hostname included). This may be particularly useful when using a CDN to host your application.</p>                                                                                                                                                                                                                                                      |
| CI                   | 🔶          | ✅          | When set to `true`, Create React App treats warnings as failures in the build. It also makes the test runner non-watching. Most CIs set this flag by default.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| REACT\_EDITOR        | ✅           | ❌          | When an app crashes in development, you will see an error overlay with clickable stack trace. When you click on it, Create React App will try to determine the editor you are using based on currently running processes, and open the relevant source file. You can [send a pull request to detect your editor of choice](https://github.com/facebookincubator/create-react-app/issues/2636). Setting this environment variable overrides the automatic detection. If you do it, make sure your systems [PATH](https://en.wikipedia.org/wiki/PATH\_\(variable\)) environment variable points to your editor’s bin folder. You can also set it to `none` to disable it completely. |
| CHOKIDAR\_USEPOLLING | ✅           | ❌          | When set to `true`, the watcher runs in polling mode, as necessary inside a VM. Use this option if `npm start` isn't detecting changes.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| GENERATE\_SOURCEMAP  | ❌           | ✅          | When set to `false`, source maps are not generated for a production build. This solves OOM issues on some smaller machines.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| NODE\_PATH           | ✅           | ✅          | Same as [`NODE_PATH`](https://nodejs.org/api/modules.html#modules\_loading\_from\_the\_global\_folders) [in Node.js](https://nodejs.org/api/modules.html#modules\_loading\_from\_the\_global\_folders), but only relative folders are allowed. Can be handy for emulating a mono-repo setup by setting `NODE_PATH=src`.                                                                                                                                                                                                                                                                                                                                                            |
