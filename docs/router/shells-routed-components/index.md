---
title: Shells and Routed Components
template: doc.hbs
url: /docs/router/shells-routed-components/
---

# Shells

Shells are an important concept to understand when building a new site in Juno. Conceptually, you can think of a shell as being the context in which all of your site content is loaded. In most cases, the shell contains base HTML tags (such as the `html`, `doctype`, `head`, and `body` tags) and can optionally be where you place the header and footer of your site if it requires one. 

## Creating a Shell

In reality, shells are just ViewComponents with a special flag. In order to mark a component as a shell, just add the property `shell: true` on your component definition. For example: 

```javascript
export default juno.ViewComponent.extend({
    is: 'view/shells/mobileshell/MobileShell',
    shell: true
});
```

By setting the `shell` property to `true`, we are marking this component as being eligible to be used as a shell.

## Default Shell

In addition to being able to mark a ViewComponent as being a shell, you can also mark a shell as being the default shell. By doing this, you are no longer required to specify a `shell` property in the route definition for a given route. Let's take another look at the shell we defined in the previous section

```javascript
export default juno.ViewComponent.extend({
    is: 'view/shells/mobileshell/MobileShell',
    shell: true
    defaultShell: true
}); 
```

As you can see above, we have marked this shell as our default shell. This means that we no longer have to specify the `shell` property in our route definition.

## Shell Lookup
When a route is hit that matches a pattern specified in our `router.js` file, Juno needs to know which shell it should render the `routedComponent` into. Juno will first check if there is a `shell` property defined on the route. If there is (and the path is valid), Juno will use this shell. If not, Juno will look for another shell with the `defaultShell` property defined. If there is no `shell` defined on the route, and no `defaultShell` is found, Juno will throw an error. 


<visual><break></break></visual>
## Shell Helpers
Juno provides a few Dust helpers that are required when constructing a shell. 

`{@shellHead /}`

This helper will automatically inject the following scripts: 
- juno.min.js
- The routed component's JS bundle

This helper should be placed in the `<head>` section of your shell. 

`{@RoutedComponent /}`

The routed component helper is what places the routed component definition into your shell. Under the hood it utilizes the `{@ViewComponent}` helper with the name of `routedComponent` to inject the routed component into your shell. 


<visual><break></break></visual>
## The Routed Component

The routed component in Juno is another important concept to grasp. In order to understand it better, let's take a look at a sample entry in our `router.js` file: 

```javascript
juno.registerRoutes({
    { pattern: '/product/:style', component: 'view/layouts/product/Product' }
});
```

Here, whenever the `/product/:style` URI is matched, we tell Juno that we want to route to the component located at `view/layouts/product/Product`. At this point, we refer to this component internally as the `routedComponent`. 

Juno will provide a reference to this `routedComponent` to our shell (either defined explicitly, or as a default shell). 

## Caveats
The one caveat with a ViewComponent marked as a shell is that it cannot be re-rendered on the client. Because shells often contain critical information about a page (usually in the page's `head` section), as well as the serialized Handoff data, we have decided to disallow re-renders of the shell on the client. 

This means that if you call a `set()` on a ViewComponent that is marked as a shell, Juno will set the data on the component instance, but will not trigger a DOM update. 
