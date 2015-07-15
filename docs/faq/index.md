---
title: Frequently Asked Questions
template: doc.hbs
url: /docs/faq/
wip: true
---

# Frequently Asked Questions

## Is Juno a Single Page App framework? 
Not really. Although operating in SPA mode is central to the performance benefits of Juno, it is not limited to single page applications. For example, you can build a site using the Juno framework that will operate as a standard, non-SPA site. Juno will attempt to use SPA mode if it is supported in the requesting browser, but if not it will simply render HTML pages and serve them as you normally would.

Additionally, since Juno provides asset bundling, build support, and so forth, it provides much more than a typical SPA framework.   

<visual><break></break></visual>

## What is the Juno Tech Stack? 
Javascript, Node.js, Express, NPM, Webpack [MORE]

<visual><break></break></visual>

## What tools come with it? 
Juno Commander, Dustbuster, Copilot [NEEDS MORE INFO]

<visual><break></break></visual>

## Does it work with older browsers? 
Yes. Juno detects whether the requesting browser supports some of the more advanced elements in modern browsers that support SPA mode. If it does, the pages are rendered in SPA mode. If not, static HTML pages are automatically transferred just like they normally are when a site is not built as a single page app.

<visual><break></break></visual>

## Does it support browser history? Back button? 
Yes. One of the challenges of SPA-based applications is maintaining browser history. Since Juno renders all pages server side, the framework is able to maintain history within a session. In modern browsers, Juno uses the history object and the `history.pushState` and `history.replaceState` methods. 

<visual><break></break></visual>

## Why not React, Angular, Backbone, Ember, and so forth? 
Could you use React instead of Juno? Absolutely. Angular? Without question. Ember? Go crazy. In fact, you can probably use them together with a bit of hackery. 

Truth be told, in some applications, a library like React is definitely worth considering. But in situations where you just need a quick way to spin up a new experience or prototype and guarantee that the fundamentals are built in, Juno is a great “batteries included” option. It’s good to remember that React is focused strictly on the view layer, which leaves a lot of implementation to be done at the other levels of the stack. 

Here’s an example: In the Nike context, like a lot of developers you might be partial to Angular.js. Angular is exceptionally popular, and for good reason – it’s got a great support community, it’s well-documented, and it performs very well on sites all across the web (and the Nike ecosystem). However, if you want to make it easy for search engines to crawl your content in SPA mode, you’re going to have to add complexity via a solution like Phantom.js, and then host and continually maintain a set of static pages - somewhere. Juno makes it much simpler to implement a quick website than something like Angular with Phantom –with server-side page renders it gives you support for the fundamentals of SEO right out of the gate. 

On the other hand, if you are looking for a solution that is focused on one-way flow for data binding, there are other JS libraries that might be a better fit. In this example, React provides strong support for this, where Juno is not designed with it so closely in mind. The downside however is you won’t get the benefits of having a one-stop system for navigation, data management, asynchronous component loading, bundling and delivery that Juno provides. 

Essentially, React requires an additional framework around it, where Juno provides that framework. React doesn’t handle things like navigation, data transfer, component state, or an asynchronous render pipeline that Juno does. Where Juno says, “I have this request. Go fetch the data,” view frameworks like React have to spend a lot of cycles managing state between server and client.

You can implement isomorphic patterns in other libraries, but it typically involves building supporting tools and significant architecture to be at a competitive level. For building microsites or prototypes, these full frameworks and development methods can be daunting at best, and wasteful at worst. Juno bridges that gap. 

<visual><break></break></visual>

## Is Juno similar to libraries I already know and love?
If you are a React or Enyo developer, the patterns and syntax will be familiar to you. The framework design of Juno was inspired in part by React.js, Enyo.js, Ember.js, and a few other libraries that use similar design patterns. For example, actions, polymer naming conventions, and so forth are similar concepts that you are likely already familiar with.

<visual><break></break></visual>

## Is Juno SEO-friendly? 
Yes. Unlike the headless browser model where pages are statically rendered and stored in a separate bucket where redirected search crawlers can access each page, the Juno framework assembles all of the views server-side, supporting full SEO access immediately. 

<visual><break></break></visual>

## Why not just use Google’s new JS support (“hash bang”) for SEO? 
Although Google has recently added support for JS (Angular specifically) via scripted HTML snapshots, Google is not the only search engine/crawler in town, particularly globally. Additionally, Google requires that your URLs use a very specific syntax to work (“hash bang” or #!), which may not fit with your URL strategy. Google also recommends in some cases using a headless browser strategy, for which Juno eliminates the need.

### So SEO all taken care of then? 
No. You will still need to follow the fundamentals of good dev practice in page and component construction, using appropriate naming and content semantics, setting up canonical references, and so forth. There are crossovers in SEO with Accessibility as well; you should work with your SEO and Accessibility partners to develop a well-formed strategy. 

<visual><break></break></visual>

## Accessibility Support
Since each state in Juno is rendered as an HTML page server-side, it is much easier to develop with accessibility in mind. The content needed for screen readers is delivered with each render, so there is no work required to keep the content up-to-date.

<visual><break></break></visual>

## How does Juno handle blocking events? 
Juno allows you to load components asynchronously, which means that you don’t have to wait for blocking components to finish loading. This opens the doors to finely tailoring the experience to load in a consistent, fine-tuned order that reflects the consumer’s experience priorities.

<visual><break></break></visual>

## Does Juno require ES6 / ES2015?
The application is built on ES2015, but we transpile it to ES5 to provide backwards compatibility.

