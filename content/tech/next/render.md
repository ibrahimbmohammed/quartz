
### Web vitals terminology


-   **Time To First Byte (TTFB)** - The time it takes for the ***client to receive the first byte of content***.
-   **First Paint (FP)** - The time when the ***first pixel gets visible to the user***.
-   **First Contentful Paint (FCP)** - The time it takes for the first piece of content to be visible.
-   **Largest Contentful Paint (LCP)** - The time it takes for the main content of the page to be loaded.
-   **Time To Interactive (TTI)** -The time at which the page becomes interactive and reliably responds to user events.

time to first byte (TTFB)
the time it takes the client to recieve the first byte of the content

first paint(FP)
the time the first pixel gets visible to the user

lcp 
the time it takes for the main content to be visible.

ttfb
fp
ttfb
fp


### Client-Side Rendering (CSR)

Client-side rendering, mostly with [create-react-app](https://reactjs.org/docs/create-a-new-react-app.html) or other similar starters, was the default way for building React apps for some time before meta-frameworks like Next.js and Remix came along.

With CSR, the server only provides barebones HTML for every page containing the necessary script and link tags. Once the relevant JavaScript is downloaded to the browser. React renders the tree and generates all the DOM nodes. All the logic for routing and data fetching is handled by the client-side JavaScript as well.

To see how it works, let’s consider that we are rendering the below app:

```jsx
// App.jsx
<Layout>	
 <Navbar />	
 <Sidebar />	
  <RightPane>		
   <Post />		
    <Comments />	
   </RightPane>
</Layout>
```

![[future-of-rendering-csr-network-graph-1200 1.webp]]


![[src_videos_future-of-rendering-csr-demo.webm]]

So CSR apps have a fast time to first byte because they rely on mostly static assets. However, users must stare at a blank screen until the relevant JavaScript is downloaded. Even after that, most real-world apps would need to fetch data from an API to display relevant data to the users, which leads to a very slow LCP.

#### Advantages of CSR

-   Since the Client-side rendering architecture comprises static files, it can be very easily served via a CDN.
-   All the rendering is done on the client hence CSR allows us to have navigations without full page refreshes, providing a good UX.
-   The time to the first byte is fast hence the browser can immediately start loading the fonts, CSS, and JavaScript.

#### Problems with CSR:

-   Since all the content is rendered on the client, **performance** takes a big hit because the users first need to download and process it to see the content on the page.
-   Client-side rendering applications usually **fetch the data** they need on the component mount, leading to a bad user experience since they would be greeted with a bunch of loaders on the initial page load. Also, this can get worse if child components have data fetching requirements, but they are not rendered until their parents have fetched all the data, which can lead to a cascade of loaders and a bad network waterfall.
-   Lastly, **SEO** is a problem with client-side rendering applications because web crawlers can easily read the server-rendered HTML, but they might not wait to download all the JavaScript bundles, execute them and wait for the client-side data fetching waterfalls to finish, which can lead to improper indexing.




### Server-side rendering

The way that server-side rendering works in React right now is:

-   We fetch relevant data and run the client-side JavaScript on the server for the page via [`renderToString`](https://reactjs.org/docs/react-dom-server.html#rendertostring), which gives us all the HTML necessary for displaying a page.
-   This HTML is then served to the client, leading to a fast First Contentful Paint.
-   But we’re not done yet; we still need to download and execute the client-side JavaScript to connect the JavaScript logic to the server-generated HTML to make the page interactive (this process is what we call “hydration”). If you are further interested in understanding why rehydration is necessary and how it works, check out [The Perils of Rehydration article by Josh](https://www.joshwcomeau.com/react/the-perils-of-rehydration/).

To better understand how it works, let’s look at the lifecycle of the same app that we saw in the previous section with SSR:


![[src_videos_future-of-rendering-ssr-demo.webm]]



![[future-of-rendering-ssr-network-graph-1200.webp]]



So with SSR, we get a good FCP and LCP, but the TTFB suffers because we have to fetch data on the server and then convert it to HTML string.

Now you might ask where Next.js’ SSG/ISR fits here. They also have to go through the same process we saw above. The only difference is that they don’t suffer from a slow Time To First byte because the HTML is either generated at build time or is generated and cached incrementally as requests come in.

![[future-of-rendering-ssg-isr-network-graph-1200.webp]]


But SSG/ISR is not a silver bullet; in my experience, they work best for public pages but for pages that change based on the user’s logged-in status or some other cookies stored on the browser, you would have to use SSR.

#### Advantages of SSR

-   Unlike CSR, SEO is much better since all the HTML is pre-generated from the server and web crawlers have no problem crawling through that.
-   The FCP and LCP are pretty fast. Hence the user has something to see instead of looking at a blank screen like in the case of CSR apps.

#### Problems with SSR

-   Since we are rendering the page on the server first with every request and have to wait for the data requirements for the page, it can lead to a **slow TTFB,** which can keep the users waiting to look at the browser spinner. This can happen for multiple reasons, including unoptimized server code or many simultaneous server requests.
    
    Although would also like to add that frameworks like Next.js somewhat solve this problem by allowing you to generate pages ahead of time and caching them on the server with techniques like SSG (Static site generation) and ISR (Incremental static site generation).
    
-   Lastly, even though the initial load is fast, the users still have to pay to download all the JavaScript for the page and process it so that the page can be rehydrated and become interactive.


### Streaming SSR

One cool thing about browsers is that they can receive HTML via HTTP streams. Streaming allows the web server to send data to a client over a single HTTP connection that can remain open indefinitely. So you can load data on the browsers over a network in multiple chunks, which are loaded out of order parallel to rendering.

#### Streaming before React 18

Streaming rendering isn’t something brand new in React 18. In fact, it has existed since React 16. React 16 had a method called [`renderToNodeStream`](https://reactjs.org/docs/react-dom-server.html#rendertonodestream) which, unlike [`renderToString`](https://reactjs.org/docs/react-dom-server.html#rendertostring), rendered the frontend as an HTTP stream to the browser.

This allowed you to send down HTML in chunks parallel to rendering it, enabling a faster time to first byte and largest contentful paint for your users since the initial markup arrives in the browser sooner.

Max Stoiber illustrated the difference nicely in [his article about how they used it at Spectrum](https://mxstbr.com/thoughts/streaming-ssr/).


![[future-of-rendering-max-stoiber-streaming-netwrok-graph-1200.webp]]

#### Streaming SSR with React 18

React 18 deprecates the `renderToNodeStream` API in favor of a newer API called [`renderToPipeableStream`](https://reactjs.org/docs/react-dom-server.html#rendertopipeablestream), which unlocks some new features with Suspense that allow you to break down your app into smaller independent units that can go through the steps that we saw with SSR independently. This is possible because of two major features added with Suspense:

-   Streaming HTML on the server
-   Selective hydration on the client

**Streaming HTML on the server**

As mentioned previously, the SSR before React 18 was an all or nothing approach; first, you needed to fetch any data requirements that the page had, generate the HTML, and then send it to the client. This is no longer the case, thanks to [HTTP streaming](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API).

The way it will work with React 18 is that you can wrap the components that might take a longer time to load and are not immediately required on screen in Suspense. Again to understand how it works, let’s assume that the Comments API is slow, so we wrap the Comments component in Suspense.

```jsx
<Layout>

<NavBar />

<Sidebar />

<RightPane>

<Post />

<Suspense fallback={<Spinner />}>

<Comments />

</Suspense>

</RightPane>

</Layout>;
```

This way, comments are not there in the initial HTML, and the user gets the fallback spinner in its place initially:

```html
<main>  
<nav>
<!--NavBar -->
<a href="/">Home</a>
</nav> 
<aside>  
<!-- Sidebar -->
<a href="/profile">Profile</a> 
</aside> 
<article>    
<!-- Post -->    
<p>Hello world</p>  
</article>  
<section id="comments-spinner">
<!-- Spinner -->    
<img width=400 src="spinner.gif" alt="Loading..." />
</section>
</main>
```

Finally, when the data is ready for Comments on the server, React will send minimal HTML to the same stream with an inline script tag to put the HTML in the right place:

```html
<div hidden id="comments">
<!-- Comments -->  
<p>First comment</p>  
<p>Second comment</p>
</div>
<script>
// This implementation is slightly simplified
document.getElementById('sections-spinner').replaceChildren(    document.getElementById('comments')  );
</script>
```

Hence this solves the first problem since we now don’t have to wait for all the data to be fetched on the server and the browser can start rendering the rest of the app, even if some parts are not ready.

**Selective hydration**

Even though the HTML got streamed, the page won’t be interactive unless the whole JavaScript for the page is downloaded. That’s where selective hydration comes in.

One way to avoid large bundles on a page during client-side rendering was code-splitting via `React.lazy`. It specifies that a particular piece of your app doesn’t need to load synchronously, and your bundler would have split it off into a separate script tag.

The limitation with `React.lazy` was that it doesn’t work with server-side rendering, but now with React 18, `<Suspense>` apart from allowing you to stream the HTML, it also lets you unblock hydration for the rest of the app.

So now, `React.lazy` works out of the box on the server. When you wrap your lazy component in `<Suspense>`, you not only tell React that you want it to be streamed but also allow the rest of hydrated even if the component wrapped in `<Suspense>` is still being streamed. This also solves the second problem that we saw in traditional server-side rendering. You no longer have to wait for all the JavaScript to be downloaded before you start hydrating.

Again, let’s look at the lifecycle of the app with Comments wrapped in Suspense, and using the new Suspense Architecture

```jsx
<Layout>  <NavBar />  <Sidebar />  <RightPane>    <Post />    <Suspense fallback={<Spinner />}>      <Comments />    </Suspense>  </RightPane></Layout>
```

![[src_videos_future-of-rendering-streaming-ssr-with-suspense-demo.webm]]

![Streaming SSR with Suspense network graph](https://prateeksurana.me/img/future-of-rendering-streaming-ssr-with-suspense-network-graph-768.jpeg)



Again this is a very contrived example, but what it is trying to demonstrate is that with Suspense a lot of stuff that was happening serially now happens in parallel.

Which helps us in not only getting a faster time to first byte since the HTML was streamed but also, the users don’t have to wait for all the JavaScript to be downloaded to be able to start interacting with the app. Other benefits about streaming rendering that I could not include in this network graph is that it also helps load other assets (CSS, JavaScript, fonts, etc.) as soon as the page starts streaming, helping parallelizing even more requests, as [Ryan mentioned in his talk](https://youtu.be/95B8mnhzoCM?t=1260).

Another cool thing is that if you have multiple components wrapped in Suspense and haven’t been hydrated on the client yet, but the user starts interacting with one of them, React will prioritize hydrating that component first. You can check this out and all the things discussed above in more detail in the [New Suspense Architecture discussion](https://github.com/reactwg/react-18/discussions/37).


0001081479