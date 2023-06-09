---
description: Some core features that come in-built with NextJS
---

# 🔑 Key Features

## Server-Side Rendering (SSR)

Server Side Rendering or **SSR** is all about preparing the content on the server side, rather than on the client. If we take a standard React app built with just React, and inspect the source code of a loaded React app page, we will notice that the page is pretty empty from the start. We will only have a basic HTML skeleton, an entry point and a `div` with an `id` `root`, into which the React app is typically loaded and rendered. Since React is a client side library, all this rendering happens on the client side, i.e, on the user's browser.

As a result, the actual HTML code send by  the server to the client (when a user visits a page) is actually pretty empty. This is not necessarily a big problem, but it depends in what kind of application you have built/are building.

For example, if your app fetches some data that is rendered onto the page as a list, the  user might initially see a loading state and occasionally some flickering of the page for a fraction of a second whilst the request is on it's way. Fetching of data only begins once the JavaScript code is executed on the client. Then again we have to wait for the response data of the outgoing request, because the page we requested did not contain the data. This is not necessarily a big problem, but it might not be the user experience we want to provide.

### Search Engine Optimization (SEO)

This can also be a problem is **Search Engine Optimization** (**SEO**) matters to you. This does not matter for all pages. For example, if we have an admin dashboard, which can only be reached via logging in, SEO does not matter there because search engines will never see that dashboard. If we have a public facing page with a lot of content that should be found through search engines, then of course SEO does matter.

In a basic React app, the search engine crawlers will only see the basic, almost empty HTML template, which we get from the server. This content is not picked up by search engine crawlers and that can be a problem, meaning your website will not show up at the top results when searching for it via a search engine. This is where server side rendering helps us. If that page is pre-rendered on the server, and the data is fetched on the server itself, then a finished page would be served to our users and also to the search engine crawlers. With this, our users will not see the flickering loading state on the screen and the search engine crawlers would see our page content. This is the problem server side rendering solves, it allows us to pre-render React pages, components on a server.

### ReactJS SSR

ReactJS actually has built in features that allows us to add server side rendering to our applications, but it can be tricky to get that right. It also requires extra setup from our side.&#x20;

With NextJS, this becomes easier since it comes with built-in SSR. It automatically pre-renders our pages, so to say if we built a basic standard NextJS app without any extra setup from our side and visit that page, it would be pre-rendered on the server by default out of the box. If we inspect this, we can see there's a pre-rendered HTML rather than an almost empty HTML skeleton.

## File based routing

In traditional React apps, we do not have router in-built, we need to add a third-party library called `react-router` to add routing to our app. This is a great package, but we do have to write extra code, and with that more files. We typically store the routes as separate files, and hence the number of routes we have in our app, thee same number of files for the same in our code repository.

But with NextJS, we can get rid of the in-code route definition. In NextJS, we define routes and pages with files and folder. We have a special **`pages`** folder in NextJS apps, which has to be named "pages". With this folder, we are defining the routes and paths our page supports. This allows us to get rid of that extra code, hence we write less code, we have less work and is a highly understandable concept since that is how we all started with web development.

NextJS also supports features, like nested routes or dynamic routes with dynamic parameters.

## Fullstack capabilities

NextJS also allows us to add back-end code to our React apps. With our client side code, along with some pre-rendered server side code, we can add standalone back-end code. With NextJS, it is easy to add our own back-end API into our project using NodeJS code. So we can easily add such code to our Next React apps when using NextJS, and that allows us to add code for storing to  a database or to files, getting data from there, authentication, etc. We would need to know some NodeJS for this arguably, but then we would know that already since we are building out own back-end! :P

So with this, we don't have to build a standalone REST API project, but instead we can work with one project, i.e, our NextJS project, add all the client side code, server side rendered code, our React user interface, and also blend-in our back-end API code. This is why NextJS is amazing.

![Key features of NextJS](<../.gitbook/assets/Screenshot 2021-06-29 at 11.37.27.png>)
