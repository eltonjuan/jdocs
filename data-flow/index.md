---
title: Data Flow
template: doc.hbs
url: /docs/data-flow/
wip: true
---

# Data Flow

The ViewComponent data flow model is a simple parent-child pattern.

<visual><break></break></visual>

`Product.dust`
```dust
<div class="product-layout">
    {@ViewComponent is="view/components/productcard/ProductCard" product=productData /}
    {@ViewComponent is="view/components/productdetail/ProductDetail" product=productData /}
</div>
```
The child components are created with Juno's `{@ViewComponent/}` Dust Helper.

<visual><break></break></visual>

`Product.js`
```javascript
import {product as productAPI} from 'api/commerce/product/Product';
export default juno.ViewComponent.extend({
  is: 'view/layouts/product/Product',
  create(req) {
    productAPI(req.params.style, this).then((data) => {
      this.set('productData', data);
      this.render();
    });
  }
});
```
In the `create()` method a Juno Service is used to retrieve data from an external data service. When the data is returned the `set()` method is used to set `productData` on the component instance. In the example above it's also important to note that anytime you use the `create()` method, you must call `render()` again.

Any data put into the defaults (either directly using `this` or with `.set()`) is available on the Dust template. We recommend limiting your use of `set()` for best performance since it triggers a re-render.</sup>

<visual><break></break></visual>

`ProductCard.dust`
```html
<div class="product-card-component ncss-brand">
    <h4>{product.title|s}</h4>
    <h2>{product.bignbold|s}</h2>
    <h4>{product.currentRetailPrice}</h4>
    {@ViewComponent is="view/pdp/components/buy/Buy" product=product /}
</div>
```
 Inside ProductCard, we can access the product data using dot notation.

<visual><break></break></visual>

####Best Practices
When building your Juno-powered site, it's very important to think about creating a design pattern that promotes a clean data flow and optimal performance. Minimal networks calls, knowing when to call `set()`, and logical component composition are other [advanced performance](/advanced-performance) tips we recommend.

