---
title: Inline Dust Helpers
template: doc.hbs
url: /docs/inline-dust-helpers/
wip: true
---

# Inline Dust Helpers
Let's say you want to write a custom format function to format some text that you're using in a timer. Meet your new friend, the Inline Dust Context Helper.

Context helpers are passed several parameters to provide information about their template and context. The full signature of a context helper is:
```
function(chunk, context, bodies, params) {}
```

## Usage
In our Coundown.dust file we have a block of Dust logic that first checks to see if timer exists and if it does, then calls the format function inline to render properly formatted hours, minutes and seconds.  
```html
// Countdown.dust
{?timer}
  {#format time=time.hours /}
  <span class="separator ml2 mr2 text-color-grey">:</span>
  {#format time=time.minutes /}
  <span class="separator ml2 mr2 text-color-grey">:</span>
  {#format time=time.seconds /}
{/timer}
```

The hours, minutes, and seconds are passed as params to the format function, which then returns the formatted value in the form of a chunk. This manually overrides the output of each {#format} section, generating a nice formatted display like 00:01:45.
```javascript
// Countdown.js
format(chunk, context, bodies, params) {
  let t = params.time.toString();
  chunk.write(t.length === 1 ? '0' + t : t);
},
```

See the official Dust.js [Context Helpers](http://www.dustjs.com/guides/context-helpers/) guide for more info.
