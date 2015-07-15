---
title: Introduction
template: doc.hbs
url: /docs/introduction/
---
# Introduction

&nbsp;
## What is Juno?
Juno is an isomorphic JavaScript framework that provides a fast, simple way to create new web experiences. Juno's component architecture allows you to create modular, composable, user interfaces that promote encapsulation and code re-use. Juno is lightweight and highly performant, focused in delivering pages as fast as possible.

<visual><break></break></visual>
## Why Juno? 
In order to understand why you might use Juno over another web framework, it's important to understand the history behind its creation and the problems that Juno tries to solve. 

Juno was originally developed for [Nike.com](Nike.com). Due to the size of Nike's website as well as its global presence, we had a unique set of challenges that the modern web frameworks at the time didn't address. Specifically, we needed: 
- Full SEO Support 
- Client rendering (SPA) for modern browsers
- Component architecture
- Speed

With most modern MVC frameworks, the bulk of the application logic lives in the client, and talks to an API for data. The server will typically render a blank page or shell and then rely on the client to render the rest of the markup. There are many benefits to this approach, but there are also certain issues that prevented us from using this approach for Nike.com. 

### SEO
An application that runs only in the client cannot serve HTML to bots and crawlers. Some search engines are beginning to build in support for JavaScript, but with Nike's international presence, we couldn't rely on this being the case for search engines like Baidu (CN) and Naver (KR). There are some workarounds, such as pre-rendering your pages and serving them out of static buckets to crawlers, but these workarounds are cumbersome and require engineering effort. 

### Performance 
Another concern of ours was performance and perceived performance. Because the server only renders a blank page, and relies on the client-side JavaScript to render the rest of the page, there could be a few seconds where users see a blank page, or a loading spinner while the client JavaScript is executing. In addition, you end up delivering larger JavaScript/CSS bundles to the client, leading to longer initial page loads. 

There are many metrics that show direct correlations between longer page load times and decreased conversion, so it was important for us to deliver pages to our customers as fast as possible. 

<visual><break></break></visual>
## The Isomorphic Way
Isomorphic JavaScript is nothing new, with frameworks such as [Meteor](https://www.meteor.com) creating abstractions that allow for JavaScript to be ignorant of the environment it is being executed in. Some of these abstractions include: 
- Routing
- Fetching and persisting data
- Rendering Views
- Bundling assets

We wanted to provide the same abstractions that create a consistent set of APIs that can be used across the server and client in a lightweight framework with a focus on UI modules, called components, and very fast page load times.

Isomorphic JavaScript allows us to render initial page loads on the server, and then switch over to client routing / navigation for modern browsers. This solves our problem of SEO support because crawlers will be served full page markup from the Node.js server. 

Because we are using Node.js to render Dust templates, markup generation is blazing fast. In most cases, Juno is able to render full page markup in less than 10ms. This, combined with our dynamic asset bundler (known as Revelation), allow us to highly optimize initial page loads (real performance), and then give our users the benefit of client navigation (perceived performance).