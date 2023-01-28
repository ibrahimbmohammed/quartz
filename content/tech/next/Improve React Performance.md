# ⚛️ Top 7 Tweaks and Tricks to Improve React Performance

If you don’t see the performance issues yet, it doesn't mean they don’t exist 😄

![](https://miro.medium.com/max/1400/1*Ig_zN5BK57NAlZxIK6l4Bw.png)

# Content

-   [**Intro**](https://itnext.io/%EF%B8%8F-top-7-tweaks-and-tricks-to-improve-react-performance-8957bab33266#ecaa)
-   [**1. Troubleshooting the redundant rerender**](https://itnext.io/%EF%B8%8F-top-7-tweaks-and-tricks-to-improve-react-performance-8957bab33266#d081)
-   [**2. Reducing the rerender by splitting the component**](https://itnext.io/%EF%B8%8F-top-7-tweaks-and-tricks-to-improve-react-performance-8957bab33266#3bc7)
-   [**3. What is instance re-creation and how to avoid it?**](https://itnext.io/%EF%B8%8F-top-7-tweaks-and-tricks-to-improve-react-performance-8957bab33266#31a7)
-   [**4. Don’t be lazy with Lazy loading**](https://itnext.io/%EF%B8%8F-top-7-tweaks-and-tricks-to-improve-react-performance-8957bab33266#6b0f)
-   [**5. Why and When to use React fragments?**](https://itnext.io/%EF%B8%8F-top-7-tweaks-and-tricks-to-improve-react-performance-8957bab33266#f6f3)
-   [**6. Avoid using the index as a key in listed elements**](https://itnext.io/%EF%B8%8F-top-7-tweaks-and-tricks-to-improve-react-performance-8957bab33266#0d12)
-   [**7. Avoid spreading props**](https://itnext.io/%EF%B8%8F-top-7-tweaks-and-tricks-to-improve-react-performance-8957bab33266#2170)
-   [**Conclusion**](https://itnext.io/%EF%B8%8F-top-7-tweaks-and-tricks-to-improve-react-performance-8957bab33266#b5d6)
-   [**Learn More**](https://itnext.io/%EF%B8%8F-top-7-tweaks-and-tricks-to-improve-react-performance-8957bab33266#7a38)

# Intro

Some developers that just started to learn React, or switched to React from other frameworks, may not pay too much attention to performance in the beginning. As it requires some time to discover the performance downside of the newly learned framework. Later on, due to a lack of experience, such developers are making small mistakes in writing the code, which will be eventually accumulated and cause performance degradation. Also, they will have a hard time troubleshooting the problem. _Here we will explore 7 tweaks and tricks that will help to avoid most of the React performance issues for building any type of application._

# 1. Troubleshooting the redundant rerender

Most of us know how virtual DOM works, but the most important thing is to detect when a tree comparison is triggered. When we can track it, we can control our component's rerender, and eventually prevent unexpected performance flows. Surprisingly, it's not that hard to catch. First, add the [React Devtool extension](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en) to your browser.

1.  Then open the browser developer tools (In Chrome it is **Option + ⌘ + J** (on macOS), or **Shift + CTRL + J** (on Windows/Linux).
2.  Select **Components**
3.  Click on the Settings Icon
4.  And checkmark “Highlight updates when components render”

![](https://miro.medium.com/max/1200/1*zTA3H7B55_yZOdtnznH2OA.gif)

That is it, now, when we interact with the UI, it shows the green borders over the elements that are rerendered at the current moment. Knowing this, we can analyze any of our React components and refactor them to avoid unnecessary re-rendering.

# 2. Reducing the rerender by splitting the component

If we are able to reduce the number of unexpected rerendered elements, it will cover the high percentage of the performance issues in React. But we have to answer the question first: _“What triggers the rerender?”_. And the answer is straightforward — the **state changes**. Every time the component state is mutated, it will wake the tree comparison, also called **reconciliation,** and rerender the elements of **state context. State context** — is a component where such a state was initialized. Meaning, if we have a giant component with plenty of states (that are not necessary depending on each other) and one of the states is changed — it will rerender the whole component elements 🤯. This is definitely not what we want.

What will be the solution then? The workaround — is to separate the state context by moving the part of the component and some of its state, into its own child component, let's take a look at the example:

Consider we have a table component with the search filter. The search filter is a controlled input with the state that is updated after the input text is changed. Here is how it looks:

![](https://miro.medium.com/max/1400/0*No0xO1IkbYljLuut)

What happens when we start to type in the search input field?

![](https://miro.medium.com/max/1200/0*17ixTVmo83RRhUzj)

Yes, it will rerender the whole table elements. It happens because the input state context shares the same context with the table component. Now, let’s try our solution and move the input element with its state into a separate component and inject it into the table component.

![](https://miro.medium.com/max/1400/0*zTzYKGygGQL-bXxf)

![](https://miro.medium.com/max/1200/0*0ECOvfO_GoNNue_i)

Magic 🧙, the table components is not rerendered anymore. We can later enhance the feature by emitting the event from input to control when exactly we want our input to affect the table element.

> Good practice is to split components to decouple the state context, in order to avoid redundant rerenders

# 3. What is instance re-creation and how to avoid it?

We have already found out that state changes trigger the component rerender, but there is another important side-effect that we need to consider. When state changes and **reconciliation** happens **—** it will reinitialize the whole component instances with keeping the new state values. It means to us, that during **reconciliation,** all the functions instances will be recreated to be able to consider new state value. We don't need it, and in most cases, the functions can be dependent on just a few of the states. We don't want to recreate the function instance, that is not dependent on the state that changed.

And this is an opportunity to improve performance. We have a few solutions for that: **useCallback** and **useRef**. Let’s see the example:

const {someState, setSomeState} = useState('')  
const {otherState, setOtherState} = useState('')const foo = () => {console.log(someState)}

This is the most common example. We have **foo** that is dependent on the state **someState.** When **someState** changes, it will recreate the new instance of the **foo**. The issue with this code — is even when some other state changes, like **otherState,** the **foo** will be recreated as well, what we actually don’t want. We can use **useCallback** to tell React what is our function state dependency, to make it more explicit when to recreate the instance:

const {someState, setSomeState} = useState('')  
const {otherState, setOtherState} = useState('')const foo = **useCallback**(() => {console.log(someState)}, [**someState**])

In this example, we pass the dependency array to **useCallback** hook. Much better, the **foo** will avoid **otherState** changes**.**

Another option is to use **useRef**. **useRef —** you can think of it like it is the same as **useState,** but doesn't trigger component rerender (the UI will not be updated). **useRef** doesn’t have the dependencies list, so we need to pass the **someState** as **foo** property:

const {someState, setSomeState} = useState('')  
const {otherState, setOtherState} = useState('')const foo = **useRef**((**currentSomeState**) => {console.log(**currentSomeState**)}).current;

In this case, we will not recreate the **foo** instance at all**.** Conclusion:

> Use **useCallback** and **useRef** to control function instance re-creation

# 4. Don't be lazy with Lazy loading

React renders components synchronously by default. It means the component will wait until its children are rendered before rendering itself. It is not necessary to wait, especially when some of the child components are not coupled. It may cause the page to hang. Let's say we click on some navigation link, that suppose to redirect us to another page. The navigation will wait until all the page components are rendered to finish redirection. It will affect user experience, people will not wait and will just leave your website. We need to make the page content render asynchronously so it doesn't harm the navigation. And the solution is to wrap your page component into `React.lazy(()` and tell React to finish navigation and then wait for the page component to finish rendering:

const PageComponent = React.lazy(() => import('./PageComponent'));

Later we can show some loading animation when the page component is not ready yet by using `<Suspense/>`

<Suspense fallback={<div>Loading...</div>}>  
   <PageComponent />  
</Suspense>

It doesn't mean we have to use a Lazy load component everywhere, it may cause overengineering when we use it in places that don't harm the performance too much. Another scenario is some components that may be hidden in UI by default, so we don't have to wait for them. For example modal windows, dialogs, drawers, and collapsable side panels.

> Lazy load page components and hidden UI components

# **5. Why and When to use React fragments?**

It happens frequently, when we build some layout in JSX and want to group our elements, in most of the cases we use `<div>` tag. Or, for example, we have parent-child HTML tags that we want to move into a separate component:

<ul>  
    <li>Item 1</li> <--- | Want to move it to child <Li> |  
    <li>Item 2</li> <--- |                               |  
</ul>

So, when we move `<li>` into separate component like:

const **Li** = () => {  
    return (  
        <div>  
            <li>Item 1</li>  
            <li>Item 2</li>  
        </div>  
    )  
}

And change it:

<ul>  
    <**Li**/>  
</ul>

After rendering, it will look like this:

<ul>  
    <div>  
        <li>Item 1</li>    
        <li>Item 2</li>      
    </div>  
</ul>

Which will create one additional `<div>` Node that we don't need. It will make our DOM tree more nested which will slow down the **reconciliation** process. Instead, `<div>` we can wrap our child elements into the Fragment. Initially, Fragment lets you group DOM elements and after inserting it will cause only one [**reflow**](https://developers.google.com/speed/docs/insights/browser-reflow)**.** In React, Fragment will also let you reduce unnecessary Nodes. The only you need to do is to use Fragment instead of `<div>` when you want to group elements:

const **Li** = () => {  
    return (  
        <> /* or <React.Fragment>, or <Fragment>*/  
            <li>Item 1</li>  
            <li>Item 2</li>  
        </>  
    )  
}

That is it, as simple as that

> Use Fragment if you want to group the elements to reduce the number of Nodes

# 6. Avoid using the index as a key in listed elements

Everyone knows, if not, Eslint will enforce it 😜 to use a **key** in listed elements, like:

<ul>  
    <li key="1">Item 1</li>    
    <li key="2">Item 2</li>      
</ul>

The **key** in React is the unique identifier, that help React point to the right element in the list and update the correct one. If we use an index as a key in the list, such as:

<ul>  
    {[1, 2].map((val, **index**) => <li **key={index**}>Item {val}</li>)}    
</ul>

We will map the element to its index. But if we have sorting, the order of elements in the list may be changed and the initial key will not point to the correct element anymore. It can cause some unexpected side effects, you can read more [here](https://robinpokorny.com/blog/index-as-a-key-is-an-anti-pattern/).

> Always use unique id as a key in listed elements, if object doesn’t have it, you can assign in explicitly using external library, like [uid](https://www.npmjs.com/package/uid)

# 7. Avoid spreading props

This is the last tweak for today, it is already a lot😓 You must have seen it before, or even worse did it yourself — spreading props. Something like:

const Input = ({ onChange, ...props }) => (  
    <input {...props} onChange={e => onChange(e.target.value)}/>  
);

It is not only forcing you to make a guess about what properties actually `input` received, but also create a bunch of attributes in the `input` the element that you don’t necessarily need.

Make it explicit, and don't be afraid to pass as many properties as you need, you can always group them into some object:

const Input = ({ onChange, inputProps: {value, type, className} }) => (  
    <input className={className} type={type} value={value} onChange={e => onChange(e.target.value)}/>  
);

Nice, much more readable now.

> Never spread props, pass each of the property separately

# Conclusion

Alright, probably you have known about some of the tweaks as they are enforced by Eslint. But now you know why it is important to follow them. And also, you can make a performance analysis of your code that will give you room for improvement. Hope you have enjoyed reading and learned something new, clap 👏 and follow me on medium, so you don’t miss an opportunity to learn.