---
title: Service API
template: doc.hbs
url: /docs/service-api/
---

# Service API

## juno.Service

Juno Services are used to retrieve and manage data from external data services. The base Service class provides a handful of helpful functions and utility to retrieve, cache, and manipulate data.

Services are generally stateless singletons which provide functions for ViewComponents to consume.

<visual><break></break></visual>

### is `string`

Just like ViewComponents, Services require an `is` property that matches the path for the file.

```javascript
// Found in: site/services/my/service/Service.js
export default juno.Service.extend({
  is: 'services/my/service/Service'
});
```

<visual><break></break></visual>

### singleton `boolean (default: true)`

By default, Juno creates an instance of your class and exports it as a singleton when you extend the base `juno.Service` class. If your Service requires any state or functionality that would require it to be constructed, you can disable this functionality by setting the `singleton` property to `false` on the prototype.

```javascript
let MyService = juno.Service.extend({
  is: 'services/my/service/Service',
  singleton: false
});

// We now have to construct our service:
let myService = new MyService();
```

<visual><break></break></visual>

### cache `string (default: 'client')`

The cache property on Services can be set to either `client`, `server`, `all`, or `none`. Responses from `.fetchAndCache()` will be cached in memory if the platform satisfies the cache parameter.

```javascript
export default juno.Service.extend({
  is: 'services/my/service/Service',
  // Cache fetchAndCache responses on all platforms.
  cache: 'all'
});
```

<visual><break></break></visual>

### base `string (default: '')`

The base parameter can be used to set a url which will be prepended all relative requests made through `.fetch()`.

```javascript
export default juno.Service.extend({
  is: 'services/my/service/Service',
  // Cache fetchAndCache responses on all platforms.
  base: 'http://test.com',
  getUser() {
    // Makes a HTTP request to:
    // http://test.com/users/1
    return this.fetch('/users/1').then((res) => res.json());
  }
});
```

If the url passed to `.fetch()` is absolute, then the base url will not be used.

<visual><break></break></visual>

### builder()

```javascript
builder(...args: string|object) : string
```

A simple url builder that can be used to construct urls. Any string parameters are concatenated with a slash (`/`). If you pass an object as an argument, it will be turned into a query string at the end of the path.

```javascript
this.builder('custom','url','path', {query: 'yes'});
// Returns custom/url/path?query=yes
```

<visual><break></break></visual>

### fetch()

```javascript
fetch(url: string, [options: object]) : Promise
```

Built on top of the emerging [fetch specification](https://fetch.spec.whatwg.org/), the Service fetch function is an easier way to make web requests and handle responses than using an XMLHttpRequest. We provide a consistent API for both the client and server, so developers need not worry about the syntax for making HTTP requests.

The Service's fetch makes calles relative to the `base` property set in the prototype. If a VIP is found for the request, the request will be re-routed through the sidecar on the server.

Juno attaches default handlers that log errors out to the console.

```javascript
getUser(user) {
  return this.fetch(`/users/${user}`).then((response) => {
    return response.json();
  });
}
```

You may optionally pass a ViewComponent instance along with the `options` object. This will enable the framework to add tracking information about the service request to the ViewComponent, which can be viewed on the client using Juno CoPilot.

```javascript
createUser(user, VC) {
  return this.fetch(`/users/`, {
    method: 'post',
    headers: {
      'Accept': 'application/json',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(user),
    ViewComponent: VC
  }).then((response) => {
    return response.json();
  });
}
```

For more `fetch` examples, see [this repository](https://github.com/github/fetch).

<visual><break></break></visual>

### fetchAndCache()

```javascript
fetchAndCache(url: string, [options: object]) : Promise
```

The fetchAndCache function should be used when accessing data that you'd like to cache in your site experience. Only services that return JSON can be cached. Data will only be cached if the request is made a platform that satisfies the `cache` property.

```javascript
// ProductService.js
export default juno.Service.extend({
    is: 'services/product/Product',
    base: 'http://yourservice.test.com/commerce/',
    cache: 'server',
    getProduct(id, params, vc) {
      return this.fetchAndCache(this.builder(id, params), {
        ViewComponent: vc
      });
    },
});
```

Internally, `.fetchAndCache()` calls the `.fetch()` method. Because of this, both functions accept the same arguments.

<visual><break></break></visual>

### shim()

```javascript
shim(data: object, [timeout: number]) : Promise
```

With the shim method you can create a promise-based data shim. This is useful when developing new service-based experiences without requiring an external data source.

If you pass a `timeout` argument, the promise returned will be resolved in the number in milliseconds provided.

```
getUser() {
  return this.shim({
    username: 'fast-pineapple',
    name: 'Juno Core Team'
  }, 1500);
}
```

<visual><break></break></visual>

### set()

```javascript
set(key: string, value: any)
```

Set a property in the service's cache. Unlike the ViewComponent's `.set()` function, the Service class does not support passing an object.

```javascript
this.set('http://test.com/users/1', response);
```

<visual><break></break></visual>

### get()

```javascript
get(key: string) : any
```

The get function retrieves a key out of the service's cache. Because the cache for services is not directly on the instance (as it is for ViewComponents), this function is required to access data previous placed with `.set()`.

```javascript
let user = this.get('http://test.com/users/1');
```
