---
title: Events
template: doc.hbs
url: /docs/view-components/events/
wip: true
---

# Events

Juno makes eventing extremely easy. Standard [DOM events](https://developer.mozilla.org/en-US/docs/Web/Events) are supported, as well as app-specific events via the juno.EventEmitter class.

<visual><break></break></visual>

## Events Object
The events object in Juno View Components contains a key/value hash. In the example above we are listening for 2 events: a click event on elements with the .nice-button class and a change event on elements with the .nice-select class. The key is `click .nice-button` and the value is `handleButtonClick`. In this example `handleButtonClick` is the name of the function that will be called when the click event fires. Your event object should always use this `'key': 'value'` pair setup.

```javascript
events: {
  'click .nice-button': 'handleButtonClick',
  'change .nice-select': 'handleSelectChange'
}
```

<visual><break></break></visual>

## DOM Events
When building experiences in Juno, you have full access to events in the DOM.  All events in a Juno site implementation are delegated on the top node of the component.

```javascript
export default juno.ViewComponent.extend({
  is: 'view/components/header/Header',
  events: {
    'click .nice-button': 'handleButtonClick',
    'change .nice-select': 'handleSelectChange'
  },
  handleButtonClick(){
    alert('buttonClickHandled');
  },
  handleSelectChange(){
    alert('selectChangeHandled');
  }
});
```

In this example, a click event on a DOM element matching the selector `.nice-button` is placed in the events hash. If you are familiar with Backbone, this should look very familiar. The event handler in this case is a function named `handleButtonClick`, and is always the value in the events hash.


<visual><break></break></visual>

## Data providing
By using Juno's `data-provide` feature in your Dust template, you can easily pass data to an event listener. In the example below, we have set `image-id-abc` as the value of that div's `data-provide` tag. 

```html
<div class="header ncss-row bg-white">
  <div class="ncss-col-sm-12">
    <div class="js-image-wrapper" data-provide="image-id-abc">
      <img class="zoomable" src="{baseUrl}{view}{imageParam}" />
    </div>
  </div>
</div>
```

In the JavaScript file for this component you will then listen for this event and call a function called `zoomHandler`. In that function, you can use `e.provided` to get the value of the `data-provide` tag from the clicked element.

```javascript
events: {
  'click .js-image-wrapper': 'zoomHandler'
},

zoomHandler(e) {
  let data = e.provided;
  console.log(data); // this would log 'image-id-abc' to the console
}
```

<visual><break></break></visual>

## Event Bubbling
`this.bubble('eventName', eventData)`

Juno supports event bubbling up the component tree from child to parent until there is no longer a parent component. 

```javascript
/**
 * COMPONENT: ProductCardControls
 * PARENT COMPONENT: SelectorSizeModal
 */
selectedHandler(e) {
  let size = e.provided;
  if(!size.available) return;
  this.bubble('selectorSizeBasic:selected', size.size);
}
```

It's important to note that you should only bubble from components that have parents, otherwise it will fail. 

```javascript
/**
 * COMPONENT: SelectorSizeModal
 * CHILD COMPONENT: ProductCardControls 
 */
events: {
  'selectorSizeBasic:selected': 'setSizeSelected'
},

setSizeSelected(size) {
  this.set('sizeSelected', size);
}
```

So in the first code block, the `selectedHandler` function bubbles up an event called `selectorSizeBasic:selected` to it's parent component, `SelectorSizeModal` which is listening for this event in the events hash table and then calls the `setSizeSelected` function. It then uses the `size` data passed through the event to set the selected size on that component. 

You can also pass through an object as the data payload in a bubbled event like so:
```javascript
this.bubble('selectorSizeModal:change', {
  'sizeType': this.showType,
  'sizeSelected': this.sizeSelected
});
```

Bubbled events will keep bubbling up the component tree until there is no longer a parent component. So in order to manually stop this bubbling, use `return false` in your event handler.

```javascript
events: {
  'selectorSizeBasic:selected': 'setSizeSelected'
},

setSizeSelected(size) {
  this.set('sizeSelected', size);
  return false; // prevents event from bubbling any further
}
```

<visual><break></break></visual>

## Actions

<visual><break></break></visual>

## Event Waterfalling
`this.waterfall('eventName', eventData)`

Juno supports waterfalling events, which can be used if you want to send events down the component chain from parent to all it's available children. You cannot stop the waterfalled event like you can in bubbled events, so waterfall should be used with caution. 

```javascript
/**
 * COMPONENT: ProductLayout
 * CHILD COMPONENTS: ProductCard, ProductDetail, Banner
 */
updateSizeDisplay(e) {
  this.waterfall('ask:updateSizeDisplay', e);
}
```
In the above example the `ask:updateSizeDisplay` event is waterfalled to all the child components of ProductLayout. If any of those child components have children, that event will be waterfalled to those as well. 

<visual><break></break></visual>

## Global Events
`juno.events.emit('eventName', eventData);`

Global events are also supported in Juno, though we recommend limiting their use to situations that can't be solved with event bubbling or event waterfalling. 
```javascript
juno.events.emit('launch:countdown', {count: seconds});
```

Here we have a countdown launch event that we want to send globally throughout our Juno site implementation. Every component in the site can now listen for and handle this global event. In the events object, you must prefix the global event name with `global:`.

```javascript
events: {
  'global:launch:countdown': 'handleLaunchCountdown'
},

handleLaunchCountdown(options) {
  console.log('count = ' + options.count);
}
```
