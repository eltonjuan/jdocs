---
title: Isomorphic JavaScript
template: doc.hbs
url: /docs/isomorphic-javascript/
---

# Isomorphic JavaScript

We've mentioned before that Juno is an isomorphic JavaScript framework, but what exactly does that mean? 

Isomorphic JavaScript is a term that was originally coined by Charlie Robbins at Nodejitsu and was popularized by Spike Brehm of Airbnb. It refers to JavaScript code that can be executed equally both the server (Node.js) and the client (the browser).

## Abstractions
In order to achieve the concept of isomorphic JavaScript, we must create a set of abstractions that allow experience developers to write code once that will 
run anywhere. 

For example, say you wanted to access a REST API in order to retrieve a list of products to display on a given page. In the browser, you would use `XMLHttpRequest`, usually wrapped in something like jQuery's `$.get()`. On the server, you would use Node's core `http.request()` API, or a wrapper such as the `request` package. Even though the code on both the client and the server is JavaScript, this code is not portable because the API's are different depending on the environment. In order to solve for this, we must create an abstraction that functions on both the client and server. In Juno we solve this by using the upcoming `window.fetch` API that works on both the server and client. We polyfill this functionality using GitHub's [fetch](https://github.com/github/fetch) polyfill. 

On the server, we take advantage of [Express](http://www.expressjs.com) and its router for handling all of our custom route logic. On the client, since we don't have access to Express, we have to normalize our API so that our route handlers don't have to access the `req` and `res` objects directly. 

In addition to normalizing the APIs that developers access across the server and client, we must also figure out how to get all of our ViewComponent bundles(HTML, CSS, JS) to the client. One approach to this would be to just bundle everything together and deliver them to the client. For more sophisticated applications however, this would lead to very large bundles that could impact page performance. To solve this, we've developed a custom webpack bundler that creates more intelligent bundles to deliver only the assets we need to the client. 

## Component State
In addition to creating an abstraction on top of the API's that developers use, we need to figure out how we transfer component state from the server to the client. That is, ViewComponents in Juno are first instantiated on the client, and it's possible for a developer to set some kind of state on the server that must be transferred to the client, so that we can resume the state of that ViewComponent. In Juno, we do this through a process known as [Handoff](/docs/isomorphic-javascript/handoff), which is basically a serialization of the component tree, passed to the client as JSON data in the shell markup. 


<visual><break></break></visual>
## Useful Links
The links below provide further readings on the concept of isomorphic JavaScript

- [Isomorphic JavaScript: The Future of Web Apps](http://nerds.airbnb.com/isomorphic-javascript-future-web-apps/)
- [Isomophic JavaScript](http://isomorphic.net/javascript)
- [Spike Brehm's Isomorphic JavaScript tutorial](https://github.com/spikebrehm/isomorphic-tutorial)



