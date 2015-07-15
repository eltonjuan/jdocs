---
title: Handoff
template: doc.hbs
url: /docs/isomorphism/handoff/
---

# Handoff

When writing isomorphic applications, data must transition from the server to the client. In Juno, this is handled with a mechanism known as Handoff.

Handoff attempts to maintain the state of ViewComponent instances between the server and client. In general, the server starts instances of ViewComponents, and the client resumes those instances, calling their `postRender()` method. This happens completely behind the scenes, and does not require any additional configuration or development to setup. The only thing that you should be aware of the properties that are handed off with your instance.

<visual><break></break></visual>

## The Rules of Handoff

All properties on the instance are included in the Handoff. This includes any properties directly on the instance (i.e. `this.isHandedOff = true`) and properties set with the `set()` method.

When we serialize the ViewComponents for Handoff, we only include properties that satisfy the following checklist:
- Must be a primitive type (i.e. String, Number, Boolean, Array, Object).
- Must not be an instance of a function other than Array or Object.
- Must be enumerable.

Objects with cyclic references will break Handoff! If handoff cannot be completed, then no ViewComponent instances will start on the client, and an error will be thrown in the console.

<visual><break></break></visual>

## Disabling Properties

There may be cases when you wish to disable a property or an instance from Handoff. The ViewComponent prototype contains a `disableHandoff` function that allows you to ignore properties in Handoff. If you call the function with no arguments, all properties on the instance are ignored in Handoff.

<visual><break></break></visual>

## Breaking the Rules

In the event that you need to an instance of a function to hand off, you can manually serialize the instance into a value that can be handed off, and then resume the instance manually.

For example, in the component below I have an instance of the `Date` function as `this.myDate`. We can utilize the `preRender` lifecycle function to store a string representation of the `Date` instance, and then use `postRender` to resume the instance.

```javascript
preRender() {
    this._myDate = this.myDate.toString();
},

postRender() {
    this.myDate = new Date(this._myDate);
    delete this._myDate;
}
```
