---
title: Component Lifecycle
template: doc.hbs
url: /docs/component-lifecycle/
---

# Component Lifecycle

Various methods are executed at specific points in a component's lifecycle.

<visual><break></break></visual>

### Rendering: preRender()
```javascript
preRender()
```

Invoked once, both on the client and server, immediately before the initial rendering occurs. If you call `set()` within this method, `render()` will see the updated state and will be executed only once despite the change.

### Rendering: postRender()
```javascript
postRender()
```

Invoked once, only on the client (not on the server), immediately after the initial rendering occurs. At this point in the lifecycle, the component has a DOM representation which you can access via `this.el`. You can also reference your child ViewComponents in the method using `this.$`

If you want to manipulate the DOM, access the `window` object, or interface with other JavaScript libraries, perform those operations in this method.

<visual><break></break></visual>

### Updating: preUpdate()
```javascript
preUpdate()
```

Invoked immedietly before rendering when a component has new state set with either `set()` or has `update()` called. This method is not called for the initial render.

Use this as an opportunity to perform preparation before an update occurs.

### Updating: postUpdate()
```javascript
postUpdate()
```

Invoked immediately after the component's updates are flushed to the DOM. This method is not called for the initial render.

Use this as an opportunity to operate on the DOM when the component has been updated.

<visual><break></break></visual>

### Destroying: preDestroy()
```javascript
preDestroy()
```

Invoked immediately before a component is removed from the DOM.

Perform any necessary cleanup in this method, such as invalidating timers or cleaning up any DOM elements that were created in `postRender`.

### Destroying: postDestroy()
```javascript
postDestroy()
```

Invoked after a component is removed from the DOM.

The DOM node for the component is no longer present when this function is called. Additionally, the child component object `this.$` will be `null`.
