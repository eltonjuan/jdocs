---
title: UI Patterns
template: doc.hbs
url: /docs/view-components/ui-patterns/
---

# UI Patterns

In Juno, there are several design patterns that should be followed when developing your experience code. These patterns ensure that your components are easy to develop, test, and debug.

<visual><break></break></visual>

## Managing State

In Juno, all rendering is based on what we call "state". The state of a ViewComponent is actually just the properties on the ViewComponent instance. Properly managing your ViewComponents' state is the first step to ensuring you are following the best UI patterns.

You should attempt to make your ViewComponent visual representation completely based on state. What this means is that instead of utilizing jQuery or DOM operations to change the view, you set state on your ViewComponent, and that state affects the render. One of the major benefits of this approach is that it removes ambiguity around how your view's DOM ended up at any given point.

### Using `defaults()`

The `defaults()` function is used to set the initial state of your component. The function is called when the instance is constructed. The returned object is then merged into the instance.

You should set properties in `defaults()` whenever you can, and _avoid referencing undefined values_.

### Updating State

There are two approaches to updating state on your ViewComponent instance. At any point you can use the `.set()` function to set new state on your component. This function accepts either a key and a value, or an object that will be merged into the instance. Calling the function triggers the view to update with the new state.

You can also simply assign new values on your instance directly to update the state. **However,** directly assigning values will _not trigger the view to update_.

<visual><break></break></visual>

## Working With Dust

Knowing how to leverage your Dust template to accomplish your view changes has incredible benefits such as performance and predictability. Making your renders a function of your state means that debugging them is simple.

We recommend familiarizing yourself with the [Dust.js syntax](http://www.dustjs.com/docs/syntax/) before continuing.

### Changing Text

If you need to change text in your ViewComponent, you should ensure that the property is set on your ViewComponent, and then utilize the `.set()` function to change it dynamically.

A common pattern for this involves adding the default value to the return from the `defaults` function.

```javascript
defaults() {
    return {
        helloText: 'Hello'
    }
}
```

In your dust template, you can then reference the property from your `defaults` function, as the object is merged into your ViewComponent instance when it is constructed.

```html
<span>{helloText}, World!</span>
```

Once the ViewComponent has rendered, you can change the property programatically using the `.set()` function. This will trigger a re-render of the view, and will patch any differences from the render into the DOM automatically.

```javascript
this.set('helloText', 'Hola');
```

Make sure you don't put the property on the prototype, as it will then be shared between all instances of the ViewComponent.

### Class Manipulation

While it may seem like a difficult problem, manipulating classes on your ViewComponent is actually incredibly easy to accomplish with Dust templates and your component's state.

#### Adding and Removing Classes

Adding and removing classes is incredibly easy with your dust templates. You can utilize the dust exists and not-exists syntax to wrap your classes in a state conditional.

For example, you can add and remove a `hidden` class based on the `isHidden` property on the ViewComponent:

```html
<div class="{?isHidden}hidden{/isHidden}">
    My Content.
</div>
```

One of the important take-aways from this is that **dust syntax can go anywhere**. As long as the resulting markup from dust is valid, you can place your dust conditionals, helpers, and references wherever it needs to.

#### Dynamic Class Name

Setting a class name dynamically can be done with a property on the ViewComponent instance. We can use the `defaults` function to set the initial state value.

```javascript
defaults() {
    return {
        validateClasses: 'idle'
    }
}
```

We can now reference this property in our dust template to add this class to a DOM element.

```html
<input class="{validateClasses}" name="validateMe">
```

At any point, you can now update this class by using the `.set()` function. This will patch the new class `invalid` into the DOM element and remove the previous class `idle`.

```javascript
this.set('validateClasses', 'invalid');
```

<visual><break></break></visual>

## Data Fetching

It is likely that a ViewComponent on your page requires external data to properly render. All data fetching happen should occur in a Service, and not in the ViewComponent. This separates the concerns of data fetching, and data rendering, and makes your application easier to write, test, and debug.

You should ensure that the data fetch occurs in the highest component possible in the tree. Once the data is fetched, it can then be passed to any sub-components that require it.

