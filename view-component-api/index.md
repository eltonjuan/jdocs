---
title: ViewComponent API
template: doc.hbs
url: /docs/view-component-api/
---

# ViewComponent API

## juno.ViewComponent

Juno ViewComponents are the fundamental building block for user interfaces in Juno. They are designed to be composable and re-usable.

You can create a new ViewComponent by calling the `juno.ViewComponent.extend()` function and passing an object that represents the new prototype for your component.

<visual><break></break></visual>

### is `string`

ViewComponents require an `is` property that matches the path for the file.

```javascript
// Found in: site/view/my/component/Component.js
export default juno.ViewComponent.extend({
  is: 'view/my/component/Component',
});
```

<visual><break></break></visual>

### events `object`

Used to define event listeners for both the ViewComponent instance and the DOM. The keys in the objects are event strings, and the values are the names of the function on the prototype that you want to register with the event.

The event listeners are added to the instance in the constructor, and as such modifying the `events` object will have no effect.

All registered DOM events are delegated events from the root element of the ViewComponent.

```javascript
events: {
  click: 'handleClick'
}
```

#### Event Strings

The syntax for event strings is fluid and allows you to register a listener for multiple events and DOM queries. The following examples are all valid.

```javascript
events: {
  'eventName': 'handler',
  '[eventName1 eventName2]': 'handler',
  'eventName DOMQueryString': 'handler',
  '[eventName1 eventName2 eventName3] DOMQueryString': 'handler',

  // Sample:
  '[focus blur change] input.ncss-input': 'checkPlaceholder'
}
```

<visual><break></break></visual>

### bubbles `array`

A descriptive block used to define what events will be bubbled by the ViewComponent. The array should contain the names of the events.

This is useful when building shared components.

The `bubbles` array is not required, but is considered a best practice. In `__DEV__`, a warning will be thrown in the console if an event is bubbled without the corresponding event defined in the `bubbles` array. In production, the message will not appear.

```javascript
bubbles: ['component:myEvent', 'component:someBubbledEvent']

//...

this.bubble('component:someBubbledEvent');
```

<visual><break></break></visual>

### actions `array`

A descriptive block used to define what actions will be fired by the ViewComponent. The array should contain the names of the actions.

This is useful when building shared components.

The `actions` array is not required, but is considered a best practice. In `__DEV__`, a warning will be thrown in the console if an action is fired without the corresponding action name defined in the `actions` array. In production, the message will not appear.

```javascript
actions: ['onItemClicked']

//...

this.action('onItemClicked');
```

<visual><break></break></visual>

### $ `object`

The `this.$` object contains all of the ViewComponent instances that the component owns from the Dust template. These components instances are automatically added and removed by Juno.

The keys of the object are strings representing the instance names of the component (either from auto-naming, or the name provided in the dust template). The values on the object are the corresponding instances.

Using the component hash is only possible after the component tree has rendered. Therefore, using `this.$` is only available on the client.

#### Auto-naming Instances

If you do not provide a `name` parameter on the `{@ViewComponent /}` dust helper, the instance will automatically be named with the following steps:

1. Retrieve the PascalCase component name (split the `is` property with `/` and pop the last value).
2. Split the result of **(1)** by `.` and retrieve the first value.
3. Convert the result of **(2)** to camelCase.
4. Check if the name from **(3)** exists on the components object. If it does not, the naming is complete. If it does, append the number `2` to the name.
5. Repeat the check from step **(4)**, and continue incrementing the appended number until there is no component on the instance matching the name.

<visual><break></break></visual>

### create()
```javascript
create()
```

The standard `create()` function calls `render()` immedietly. As such, calling `render()` when your create logic is complete is essentially calling the super function.

```javascript
create() {
  myService.waitForData().then(data => {
    this.data = data;
    this.render();
  });
}
```

<visual><break></break></visual>

### defaults()
```javascript
defaults() : object
```

The `defaults()` function is used to set the initial state of your component. The function is called when the instance is constructed. The returned object is then merged into the instance.

The default behavior for the function is to return an empty object.

You should set properties in `defaults()` whenever you can, and avoid referencing undefined values.

```javascript
defaults () {
  return {
    headerText: 'Update your name:',
    person: {
        firstName: '',
        lastName: '',
    }
  }
}
```

Providing empty values will prevent Dust renders from warning for `undefined` and will also make your serialized component state more helpful in debugging.

<visual><break></break></visual>

### set()
```javascript
set() : Promise
set(state: object) : Promise
set(key: string, value: any) : Promise
```

Once the ViewComponent has rendered, you can change any property on the instance programatically using the `set()` function. This will trigger a re-render of the view, and will patch any differences from the render into the DOM automatically.

If you pass in an object, it will be merged into the instance.

Calling `set` with no arguments triggers a view update (see also: [update](#update-)). This is useful when you work directly with the data on the instance.

```javascript
this.set('helloText', 'Hola');
this.set({
  isHidden: false,
  moreText: '...'
});
```

The method returns a promise that will resolve when the update is complete.

The re-renders from the set method are scheduled for the next tick, so calling set multiple times in a row will result in only one update.

<visual><break></break></visual>

### update()
```javascript
update() : Promise
```

Update should be used when you want to update your state without using the `set()` method. Under the hood, the `update()` function calls `set()` with no arguments, triggering a view update.

Just like set, the method returns a promise that will resolve when the update is complete.

This function is useful when directly manipulating data on your instance.

```javascript
addNewGreeting() {
  this.myDataArr.push('Hola');
  this.update();
}
```

<visual><break></break></visual>

### bubble()
```javascript
bubble(eventName: string, ...args: any)
```

Event bubbling allows you to `emit` events upward, through the component chain via the parent component.

Calling bubble will first call `emit` (with the same arguments passed to `bubble()`) on the component instance. It will then perform the same call on the parent ViewComponent, and will continue calling up the chain until there is no more parent component. You can stop bubbling at any point by returning `true` from an event handler.

If you bubble an event, you should put the event name in the [`bubbles` array](#bubbles-array-).

```javascript
this.bubble('mycomponent:eventName', eventData)
```

By convention, you should include a namespace for the bubbled event so that event name collisions are less likely to occur.

<visual><break></break></visual>

### action()
```javascript
action(actionName: string, ...args: any)
```

Actions are a simplified mechanism that functions roughly the same as single-level event bubbling, but with a highly-declarative API. Child components can fire actions using the `action()` method, which accepts the name of the action you wish to fire, and any additional arguments that will be passed to the parent handler function.

In a dust definition for a ViewComponent, you can add an action handler by just adding a parameter to the helper. The name of the parameter should match the name of the action that will be fired by the child component, and the value should be the name of the handler function on the parent ViewComponent instance.

```html
{@ViewComponent is="mycomponent/MyComponent" onInputChanged="changedHandler" /}
```

The component `MyComponent` can then trigger the `changedHandler` function on their parent by firing the `onInputChanged` action.

```javascript
this.action('onInputChanged');
```

If you fire an action, you should put the action name in the [`actions` array](#actions-array-).

By convention, you should prefix your actions with `on` to prevent interferring with other non-action parameters.

<visual><break></break></visual>

### waterfall()
```javascript
waterfall(eventName: string, ...args: any)
```

Used to send events down the component chain from parent to all it's available children. 

You cannot stop the waterfalled event like you can in bubbled events, so waterfall _should be used with caution_. 

```javascript
this.waterfall('eventName', eventData)
```

<visual><break></break></visual>

### getCollection()
```javascript
getCollection(collection: string) : array
```

Used to pull out component collections.

```html
{@ViewComponent is="view/confirm/Confirm" collection="buyingTools" /}
```

```javascript
this.getCollection(collectionName:string)
```

<visual><break></break></visual>

### render()
```javascript
render()
```

Performs the initial render for the ViewComponent and invokes the render lifecycles for the ViewComponent. This function should be called once from the `create()` function once all operations are complete.

<visual><break></break></visual>

### renderInto()
```javascript
renderInto(element: HTMLElement) : Promise
```

Renders a given component into an DOM element. This function may only be called on anonymous component instances, and it may only be called once.

```javascript
createModal() {
  this.myModal = new MyModal();
  this.myModal.renderInto(document.body);
}
```

<visual><break></break></visual>

### destroy()
```javascript
destroy()
```

Removes a component from the DOM and invokes a component's destroy lifecycle. By default, Juno runs the `destroy()` function whenever a component is no longer present in a render.

This function may be called to dynamically destroy a component that was created in experience code. If your ViewComponent was created in the Dust template, it _cannot be destroyed with `destroy()`_. Once a component is destroyed, the component instance cannot be returned to a usable state, so use with caution.

<visual><break></break></visual>

### disableHandoff()
```javascript
disableHandoff([property: string])
```

The `disableHandoff()` function that allows you to ignore properties in Handoff. The string that you pass to the function will be blacklisted from Handoff.

If you call the function with no arguments, all properties on the instance are ignored in Handoff.

If you call `disableHandoff` on the client, it will throw an error as the Handoff has already occured.

```javascript
create: function() {
  this.disableHandoff();
  this.render();
}
```
