---
description: Let's get started with creating a NextJS project and adding pages.
---

# 🎬 Getting Started

## NextJS project

To get started with a NextJS project, we can follow the steps mentioned in [this link](https://nextjs.org/learn/basics/create-nextjs-app/setup). Alternatively, you can follow the steps I have mentioned below:

1. Install [NodeJS](https://nodejs.org/en/download/) (if not already installed)
2. Open up the terminal and navigate to the directory where you want to create the NextJS app.
3. Run the command to create the NextJS project:

```bash
#for yarn users
yarn create next-app nextjs-demo
#for npm users
npx create-next-app nextjs-demo
```

This will ask a few basic questions and then we are ready to dive into the NextJS app.

## Adding First Pages

Let's create our first pages in NextJS. For this, we will delete all the default code and files that come bundled with `create next-app`. We'll remove the the following files:

* `pages/api/hello.js`
* `styles/Home.module.css`

Let's add a new route that redirects to a `News` page, and modify the existing `index.js` file to act as the home page.

{% tabs %}
{% tab title="Homepage" %}
{% code title="pages/index.js" %}
```jsx
const Index = (props) => {
  return <h1>Home Page</h1>
}

export default Index
```
{% endcode %}
{% endtab %}

{% tab title="News Route" %}
{% code title="pages/news.js" %}
```jsx
const News = (props) => {
  return <h1>News Page</h1>
}

export default News
```
{% endcode %}
{% endtab %}
{% endtabs %}

We can now spin up the `dev server` using either of the following commands:

```bash
#for yarn users
yarn dev
#for npm users
npm run dev
```

Once the server is up, we can view our NextJS app on `localhost:3000` or `0.0.0.0:3000`.  We can also route to the `News` page by changing the URL to `localhost:3000/news` or `0.0.0.0:3000/news`.

{% hint style="info" %}
We can view the source of this page and see that it is not the empty skeleton HTML that we used to have with standard React applications, rather it contains a lot of data already in the HTML file, which is `pre-rendered` by the server. This is done by default out-of-the-box by NextJS.
{% endhint %}

It is this easy to get started with `file based routing` in NextJS apps, without any extra configuration or installation of third party libraries. This also has the added advantage of SSR which  reduces the flicker that the user sees for a fraction of a second, but more importantly, it also serves as a page that the search engine crawlers can view, thus improving SEO.

## Nested Paths & Pages

In the above example, we created the `news` route directly inside the `pages` folder. It is completely okay to do so, and nothing wrong with it, it's just that we can also create a new folder called `news` inside which we can add the code from `news.js`, but name this file as `index.js` inside of the `news` folder, which helps us with nested routes.

For example, suppose we want to have a route within the `news` route, something like `news/breaking` or `news/latest`, we would need this folder approach to creating paths and routes in our NextJS app.

{% tabs %}
{% tab title="News" %}
{% code title="pages/news/index.js" %}
```bash
const News = (props) => {
  return <h1>News Page</h1>
}

export default News

```
{% endcode %}
{% endtab %}

{% tab title="Latest" %}
{% code title="pages/news/latest.js" %}
```jsx
const Latest = (props) => {
  return <h1>Latest News</h1>
}
export default Latest

```
{% endcode %}
{% endtab %}

{% tab title="Breaking" %}
{% code title="pages/news/breaking.js" %}
```jsx
const Breaking = (props) => {
  return <h1>Breaking News</h1>
}

export default Breaking

```
{% endcode %}
{% endtab %}
{% endtabs %}

Spin up the `dev server` and route to `localhost:3000/news`, `localhost:3000/news/latest` and `localhost:3000/news/breaking` to view the different routes. All of these routes are pre-rendered on server side.

## Dynamic Pages (w/ params)

Notice in the previous example, we created two files `breaking.js` and `latest.js` as routes, but with almost identical content within. This leads to code duplication, and is not ideally a preferred approach to routing in NextJS apps. We typically would prefer to use a  dynamic route name, instead of hard-coding it as `breaking` or `latest`.

We can implement this with dynamic paths/pages. We use a special syntax to name our route files here so that NextJS can understand it. We wrap our file (route) name within square brackets `[]`. This tells NextJS that this file is a dynamic route, and should be loaded for different values in the path. Within the brackets, we need to add an identifier, which we can name according to our convenience. This tells NextJS that this page will be loaded for different values.

Let's name our dynamic page as `[newsType].js` .

### Extract dynamic parameter value

How do we extract the entered path value inside the component so that we can perform the correct action for it? NextJS gives us a special hook for this purpose, called `useRouter` from the `next/router` sub-package. It returns an object, on which we get certain pieces of data, and from there we can get the dynamic route name for our route, and hence perform the  required action as per the route name.

Let's modify our previous news example to fit in here. We will use the `query` property on the `useRouter` object along with the dynamic route name that we defined earlier within the `[]` brackets, to get the dynamic route name within the component.

{% tabs %}
{% tab title="Dynamic Route" %}
{% code title="pages/news/[newsType].js" %}
```jsx
import {useRouter} from 'next/router'

const NewsType = (props) => {
  const router = useRouter()
  const type = router.query.newsType
  
  return <h1>{type} news</h1>
}
export default NewsType

```
{% endcode %}
{% endtab %}
{% endtabs %}

We can now use the `type` value retrieved from the `router` object, to, for example, fetch the type of news from the back-end API, be it `breaking` or `latest`. Of course, we wouldn't do it this way in a real scenario, but this is just for the sake of an example.

## Linking b/w pages

To navigate between pages, we use the special component provided by NextJS, from the `next/link` sub-package, called `Link`. Using it is simple, since it requires a `href` property to provide the link to the page that we want to route to.

{% code title="pages/news/index.js" %}
```jsx
import Link from 'next/link'

const News = (props) => {
  return (
    <>
      <h1>News Page</h1>
      <ul>
        <li>
          <Link href="/news/breaking">Breaking News</Link>
        </li>
        <li>
          <Link href="/news/latest">Latest News</Link>
        </li>
      </ul>
    </>
  )
}

export default News

```
{% endcode %}

We can also route using the traditional anchor tag from HTML `<a href="">...</a>`, but that leads to a page refresh, making the user go out of our single page application, and thus defeats the purpose of creating a single page application. Hence, we should always use `Link` component provided by `next/link` to provide route links, since it prevents the browser default behavior of sending a new request to the server to go to the specified link.
