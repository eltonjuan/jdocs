---
title: Event Emitter API
template: doc.hbs
url: /docs/event-emitter-api/
disableFeedback: true
wip: true
---

# Event Emitter API

## juno.EventEmitter

The juno.EventEmitter class contains a basic event implementation. Both the `juno.ViewComponent` and `juno.Service` classes extend `juno.EventEmitter`, and as such, all of these methods are available on those instances.

An Event Emitter instance is available on `juno.events` for use as a global events bus.

<visual><break></break></visual>

### emit()
```javascript
emit(eventName: string, ...args: any)
```

Emits a specific event with `eventName` on the instance. This will call any handlers registered with `on` or `once` matching the `eventName`.

Any additional arguments that are passed to `emit` will be collected and used to call the event listeners.

```javascript
this.emit('someEvent', false, data);
```

<visual><break></break></visual>

### on()
```javascript
on(eventName: string, handler: function)
```

Listens for a specific event name, and calls a given handler function whenever the event is emitted. The handler function will be called with the additional arguments that are passed to the `emit` function.

```javascript
this.on('someEvent', (changeVisible, otherData) => {
  // ... 
});
```

<visual><break></break></visual>

### once()
```javascript
once(eventName: string, handler: function)
```

Functions the same as the `on()` function, but will only call the `handler` function once. Under the hood, this calls `off()` immedietly after the `handler` is called.

```javascript
this.once('someEvent', () => {
  // ...
});
```

<visual><break></break></visual>

## off()
```javascript
off(eventName: string, [handler: function])
```

Remove a previously-bound handler function. If no handler is specified, all handlers for the event name will be removed.

```javascript
this.off('eventName', fn);
```


