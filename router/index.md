---
title: Router
template: doc.hbs
url: /docs/router/
---

# Router 
Routing refers to the relationship between end points (URIs) and the component to render when a matching end point is requested.

When a matching pattern is found, the component in the route definition is combined with a shell, and the resulting markup is rendered to the browser. 

## Defining Routes

A route is a combination of URI and component path, representing the component you would like to render when the end point is requested. Let's take a look at a sample router file: 

```javascript
juno.registerRoutes({
  routes: [
    { pattern: 'my/cool/site/', component: 'view/components/mycoolcomponent/MyCoolComponent' }
  ]
});
```

In the code snippet above, we make a call to the `juno.registerRoutes` method which exists on the global `juno` object. Internally (on the server), the `registerRoutes` method instantiates a new `express.Router` class (see the [Express docs](http://expressjs.com/guide/routing.html)), parses the `routes` object and registers  the routes and a custom handler with Express. For more information on the `registerRoutes` method, you can check out the Juno API documentation. 

<visual><break></break></visual>

The following are all examples of valid route definitions:

```javascript
juno.registerRoutes({
  routes: [
    // basic route
    { pattern: '/', component: 'view/layouts/landing/Landing' },
    // regex patterns
    { pattern: /\/simple/, component: 'view/layouts/simple/Simple' },
    // more complex regex
    { pattern: /\/(discussion|page)\/(.+)/, component: 'view/layouts/complex/Complex' },
    // express-style params
    { pattern: '/product/:style/', component: 'view/layouts/product/Product' }
    // pattern with explicit shell (required if no `defaultShell` is set)
    { pattern: '/mobile/product/:style', component: 'view/layouts/product/Product' shell: 'view/shells/mobileshell/MobileShell' }
    // pattern with SPA nav disabled
    { pattern: '/product/checkout', component: 'view/layouts/checkout/Checkout', SPA: false}
    // specifying a `base` on which all other routes are constructed
    { base: '/product', routes: [
      // this pattern would match /product/checkout/:style
      { pattern: '/checkout/:style', component: 'view/layouts/checkout/Checkout'},
      { pattern: '/view/:style', component: 'view/layouts/product/productview/ProductView'}
    ]}
  ]
});
```

<visual><break></break></visual>
## Client Routing
If Juno is running in a modern browser (with access to the `window.history` API), Juno will perform all subsequent navigation on the client. This means you get all the benefits of server rendering (faster load times, full SEO markup, smaller app bundles) and then users with modern browsers get to take advantage of the benefits of client-side routing and rendering. This is an extremely powerful feature of Juno. 

### How does it work? 
The first render of any page always happens on the server. This allows for us to deliver initial page loads faster, accommodate robots (by providing them full page markup), and gracefully degrade in older browsers. 

Once the page is rendered and delivered to the client, the client router is instantiated and handles any subsequent page request.

#### The Rules
When performing navigation on the client, there are several rules that we follow in order to ensure that it is safe to perform a client-side navigation. The rules are as follows: 

- Does the browser support `history.pushState` ? 
  + If no, gracefully fall back to server renders.
  + If yes, hijack all link clicks (by adding a click handler to all `a` elements) and invoke client routing logic when links are clicked. 
- Do the current `routedComponent` and the requested `Component` share the same shell?
  + If no, render the requested page on the server
  + If yes, perform a client navigation

#### Routes.json
When the Juno app is started, an endpoint is created, `/js/routes.json`, that can be accessed from the client browser. This endpoint serves up a JSON file containing all of the defined routes for the application. 

Because we don't have access to Express on the client, there is logic in Juno to provide an Express-like router interface on the client. In addition, there is logic needed to transfer more the complex route patterns (as seen above) from the server to the client. 

#### Swapping out components
Once it is determined that we can successfully perform a client-side navigation, we need to swap out the current `routedComponent` (and all of its children) with the components for the requested route. In order to do this, we require that you have an element in your shell with the attribute `juno-content`. You must only have on element with this attribute, or client-side routing will break. 

Internally, we hook onto the element with the `juno-content` attribute, and insert the requested `routedComponent` into it. 

### Navigating Programatically
If you need to navigate to another page programatically, you can use the `juno.router.navigate(path)` method to do so. This is the public API for navigating using Juno's router. Juno will do a lookup on the `path` parameter and attempt a navigation. If the `path` is not found in the `routes.json` file, it will perform a hard navigation to the provided path.

Protip: Disable SPA mode for a given route by adding `SPA: false` to the route definition.