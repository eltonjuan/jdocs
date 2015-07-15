---
title: Key Concepts
template: doc.hbs
url: /docs/key-concepts/
---

# Key Concepts
Listed below are a few of the key concepts that are important to understand when getting started with the Juno framework. 

## Templates
A template, written in [Dust](http://www.dustjs.com), describes the user interface of your application. The context of your Dust template is always the data that lives on the instance of your ViewComponent. In addition to HTML, templates can contain the following: 
- Dust Expressions, such as `{someVar}` that reference a variable in the Dust context and output it to the markup.
- ViewComponent references
    + Using the `{@ViewComponent /}` helper, you can make references to other ViewComponents from within your template. 

## Router
The router in Juno translates a URI into a `routedComponent`. Juno then injects the `routedComponent` into its shell, and a page is created! See the docs on [Routing](/docs/routing/) for more information. 

## ViewComponents
ViewComponents are the basic building blocks of UI in Juno. They consist of a Stylus/LESS stylesheet, a JavaScript file (transpiled with [Babel](babeljs.io)), a Dust template, and optionally a JavaScript unit test. 

## Shells
A shell in Juno is just another ViewComponent with a special `shell` property marked as `true`. Technically, it is the HTML template into which your ViewComponent tree will be rendered. See the docs on [Shells](/docs/shells-routed-components) for more details. 

## Bundling
Juno has a dynamic, intelligent, asset bundler known as Revelation. It is a custom implementation of the [webpack](http://webpack.github.io/) module bundler. 

## Isomorphic JavaScript
Ismorphic JavaScript is a term that was coined Charlie Robbins as Nodejitsu and popularized by Spike Brehm of Airbnb. It refers to JavaScript code that can run equally well on the client or on the server. This allows you to write JavaScript once and run it anywhere. 

