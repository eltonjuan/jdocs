---
title: Advanced Performance
template: doc.hbs
url: /docs/advanced-performance/
---

# Advanced Performance

While the Juno framework will handle most of the performance considerations for you, there are a few things that you can do to ensure that your application is getting the highest performance possible.

<visual><break></break></visual>

### Know when to call `.set()`

Ensuring that your application doesn't call the `.set()` function when it shouldn't is the best way to get a higher-performance application. The `.set()` function in Juno triggers a re-render (also known as an "update"). These updates cause the entire component and all of its sub-components to recreate their markup, and perform expensive diff calculations.

If you would like to put new data on your instance, but you don't need the view to render, you can simple work with the instance directly using `this`. There is no need to wrap your data setting around `.set()` if you don't want to update the view.

When you do want Juno to update the view, you can call `.set()` as many times as you like. The method schedules a render for the next tick of the event loop, so multiple sequential calls won't degrade performance. If you want to force an update without providing any new data, you can call `.set()` with no arguments.

Also ensure that you never call `.set()` in the `postUpdate()` function as it will result in an infinite render loop!

<visual><break></break></visual>

### Don't touch the DOM.

DOM operations are expensive, and generally they're the slowest part of an applications. Use Dust whenever possible to ensure that your DOM manipulations are batched, and are run in the most effient way possible.

<visual><break></break></visual>

### Break down ViewComponents logically

Breaking your site down into too many components can be a serious performance bottleneck. Having many components on the page (espcially on pages with many data updates) will cause Juno to slow down. Our recommendation is to build your application with as few ViewComponents as possible, and start creating components when you notice that you are re-creating UI elements in multiple places.
