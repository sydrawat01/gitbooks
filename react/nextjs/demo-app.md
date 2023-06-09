---
description: Testing the waters with NextJS by building a simple application.
---

# 👩‍🔬 Demo App

## Pre-rendering & problems

We load the data from our `DUMMY_DATA` array, but in a real world scenario, this data would be coming from a back-end server, typically a database, and we would then send a `fetch` request to this back-end. We would then display this data to the user. We would typically add this with a `useEffect` hook to perform this side-effect (i.e, fetching of data from server).

Here, in this example, we will use the `useEffect` hook to demonstrate the fetching of data from the server, but not really, and instead use the dummy data to be loaded onto the `MeetupList` component. This would typically be an asynchronous task, and once the data is fetched successfully, the `setLoadedMeetups` is called to set the state for `loadedMeetups`.

{% code title="pages/index.js" %}
```jsx
import {useEffect, useState} from 'react'
import MeetupList from '../components/meetups/MeetupList'
const DUMMY_MEETUPS = [...]

const Index = () => {
  const [loadedMeetups, setLoadedMeetups] = useState([])
  useEffect(() => {
    //HTTP fetch request to back-end
    setLoadedMeetups(DUMMY_MEETUPS)
  }, [])
  
  return (
    <>
      <MeetupList meetups={loadedMeetups}/>
    </>
  )
}

export default Index
```
{% endcode %}

This would seem like the data is being loaded instantly, but at times, the user may see a loading spinner or a loading state in the front-end when the `Promise` from the `fetch` request is not yet completed. This may or may not be the kind of user experience we want to provide.

Here, we are not actually sending an HTTP request to the back-end server, but there is a difference on how this works. The `useEffect` executes **AFTER** the component was executed. So this means, when the component was rendered for the first time, `loadedMeetups` will be an empty array. Then, the `useEffect` is executed, which updates the state and thus cause the component to be executed again, it will re-render the component and the list will contain the actual data now, but we will have TWO component render cycles.

In the  first render cycle, the first time the component renders, `loadedMeetups` state will be empty, i.e, it's initial state. Two main problems are associated with this. First, the user may experience a loading spinner or state for a brief amount of time. Second, because of the two cycles, this causes a problem with **Search Engine Optimization** (**SEO**). If we viewed the page source, we would see that the actual meetup data is missing. So, the items we see on the screen are missing in the HTML file. The pre-rendered HTML page by NextJS does not wait for this second render cycle, it always takes the result of the first render cycle and returns that as the pre-rendered HTML code.

Because of this, our data is missing in the HTML code. Hence, when fetching data from the server, we might actually load an empty pre-rendered HTML code because NextJS picks up only the first render cycle and does not wait for the second one, which may result in a pretty empty page, thus hindering SEO.

NextJS has some other core features that help us tackle this very problem where we want to pre-render a page with data, but with data for which we have to wait.

## Data Fetching for Static Pages

![Default page pre-rendering & Hydration in NextJS](<../.gitbook/assets/Screenshot 2021-07-01 at 17.17.59.png>)

NextJS basically uses the snapshot of the first render cycle in our app, and that populates our HTML code. As we saw above, there are problems associated with this, mainly we might miss some crucial data. So, if we visit some route to this page, then we return that pre-rendered page but we might be missing data here. Since pre-rendering content is useful for SEO, in this case it is not.

But, after this HTML page is received, React will actually take over, the page now gets **`HYDRATED`** as the process is called, which means it will turn this into a single page application and the the `useEffect` function call might be executed, which in turn "_**hydrates**_" data into the application (data might be fetched and page might be updated). The issue here is, all this is done in the client browser, once the code is received from the server, i.e, not on the pre-rendered page. Only then we will have a fully interactive page.

If we want to pre-render a page with data so that the initially returned HTML code contains this data, we have to fine tune and configure the built-in pre-rendering performed by NextJS, and for this NextJS has two alternatives:

1. `Static Generation`
2. `Server-Side Rendering`

## Static Generation

In static generation, the page is pre-rendered at build, i.e, when we build our NextJS application for production. With this, our page is not pre-rendered on-the-fly on the server when a request reached the server, rather, it is pre-rendered when we as a developer build our site for production. This means, after our site is deployed, the pre-rendered page does not change, at-least not by default. If you then update the data and you know that the pre-rendered page needs to change, we need to start the build process and re-deploy again.

This might sound worse than it actually is because for a lot of applications, pages don't change all the time, page content doesn't change all the time, but if it does change frequently, there are other alternatives to this method, which we will see below shortly.

So, by default NextJS statically generates pages for us, but if we have fetch data in a component, where we need to wait for the data to arrive from the fetch request, we can do so by exporting a special function from inside the page component file. This will work only in the page component file we export from, not in other component files, i.e, only in the components inside of the `pages` folder. The function we export here is called **`getStaticProps`** which is like a reserver name. NextJS encounters this function, and will execute this function during the pre-rendering process. So `getStaticProps` gets called before the component function itself gets called. It has this name because it indeed prepares the props for this static component page and these props could then contain the data that this page needs.

{% code title="pages/index.js" %}
```jsx
import {useEffect, useState} from 'react'
import MeetupList from '../components/meetups/MeetupList'
const DUMMY_MEETUPS = [...]

const Index = () => {
  const [loadedMeetups, setLoadedMeetups] = useState([])
  useEffect(() => {
    //HTTP fetch request to back-end
    setLoadedMeetups(DUMMY_MEETUPS)
  }, [])
  
  return (
    <>
      <MeetupList meetups={loadedMeetups}/>
    </>
  )
}

export async function getStaticProps() {
  //fetch data from an API
}

export default Index
```
{% endcode %}

Another important note, the `getStaticProps` is allowed to be asynchronous, due to which we can return a `Promise` there, and NextJS will wait for that promise to resolve and only then execute the component function, which ideally means, it waits for the data to be fetched and returned as props, so that it can be pre-rendered into the component. This means we can load our data BEFORE the component function is executed and a pre-rendered component page is generated.

In `getStaticProps` function, we can execute any code that runs on the server, like, for example, fetch data from an API, we could access a file system here, or securely connect to a database. Any code we write in here will never end up on the client side and never execute on the client side simply because it is executed during the build process, not on the servers, and specially not on the client side.

We need to return an object from `getStaticProps`. We can return various things, but most importantly, we need to set a `props` property in this object. This `props` property will hold another object, which will be the props we receive in our page component function. With this, we can move our data fetching process from the client side to the server side, precisely speaking, to the build side rather than server side.

{% code title="pages.index.js" %}
```jsx
//import {useEffect, useState} from 'react'
import MeetupList from '../components/meetups/MeetupList'
const DUMMY_MEETUPS = [...]

const Index = (props) => {
  /* === WE CAN GET RID OF THESE LINES BELOW ===
  const [loadedMeetups, setLoadedMeetups] = useState([])
  useEffect(() => {
    //HTTP fetch request to back-end
    setLoadedMeetups(DUMMY_MEETUPS)
  }, [])
  */
  
  return (
    <>
      <MeetupList meetups={props.meetups}/>
    </>
  )
}

export async function getStaticProps () {
  //HTTP fetch request to back-end server or API
  return {
    props: {
      meetups: DUMMY_MEETUPS
    }
  }
}

export default Index
```
{% endcode %}

Now, if we spin up the development server, and inspect the source code for the HTML code, we can see that the unordered list is no longer empty, it has data that is pre-fetched in the build process. So there is no need to wait for the second render cycle and then hydrate our app, where we can directly pre-fetch the data during the build process and then pre-render it to our app.

### Static Site Generation (SSG)

Let's build our application to see which pages in our application are statically generated and the ones which are SSG based.

To run the build command:

```bash
#for yarn users
yarn build
#for npm users
npm run build
```

Once the build is complete, let's analyze the output from this build process.

![Build process output](<../.gitbook/assets/Screenshot 2021-07-02 at 09.02.47.png>)

From the output here, we can see that the build command has generated 5 static pages, out of which the root page, i.e, the `/` page is the root page, with a filled dot in front of it ⚫️. If we have a look at the legend, we see that the ⚫️ represents a SSG page, or an automatically generated Static Site page which is HTML + JSON, i.e, which uses the `getSTaticProps` method to fetch data before the page is rendered. All other pages are with an empty dot in front of them ⚪️, which means they are rendered as static HTML pages, without the pre-fetching of data in them, hence no use of `getStaticProps`method.

### Problem

The main problem that we face with SSG is that the data that we pre-fetch during the build process might  be outdated when  we deploy our website. For example, of we add more meetups to our demo app here, this pre-rendered page would not know the new meetup locations and it's data. So, if we don't add any client side fetching, our data will always be outdated. This is potentially a big problem, and we would have to re-build and re-deploy our apps again to get the latest data in the pre-redered HTML code.

The SSG approach is more suited towards applications where data does not change that frequently, one such example being a blog, where data is rarely updated, and if it does, there is not much re-build and re-deploy process associated with them.

### Solution

To overcome the above problem, where we need to get the latest data instead of the outdated data in a pre-rendered page, we can add a special property to the return object of the `getStaticProps` method in our component page. This property is called the **`revalidate`** property, which unlocks a feature called **`incremental Static Generation (SSG)`**. Revalidate requires a number, which is the number of seconds NextJS will wait,  until it regenerates this page for an incoming request. This means that the page will not only be generated during the build process, but also (the number of seconds we provide to the `revalidate` property), it will be generated on the server, at-least if there are requests from that page.

{% code title="pages/index.js" %}
```jsx
//...

export async function getStaticProps() {
  //HTTP fetch request
  return {
    props: {
      meetups: DUMMY_MEETUPS
    },
    revalidate: 10
}
```
{% endcode %}

Therefore, the number of seconds we want to use in this property depends upon the  update frequency of our data in the back-end, or the place we fetch our data from. If our data changes once in every one hour, it would be logical to set `revalidate` to 3600 seconds. If our data changes constantly, we would set it to 1, so on and so forth.

With this, we ensure that our data is not outdated, and that our page pre-renders again on the server, whenever the data changes, and hence eliminating the need to rebuild and re-deploy our apps again.

## SSR with `getServerSideProps()`

Now that we've covered SSG, let's look at **Server-Side Rendering (SSR)** with **`getServerSideProps`**.
