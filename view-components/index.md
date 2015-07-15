---
title: ViewComponents
template: doc.hbs
url: /docs/view-components/
wip: true
---

# ViewComponents

When building experiences in Juno, ViewComponents are the primary building blocks. Juno's Component implementation is best described as a View-Controller. The `juno.ViewComponent.extend()` extends the ViewComponent class, and allows you to easily hook up events and additional functionality into the component.

ViewComponents are self-contained and may be composed with great flexibility to form an entire site experience.

<visual><break></break></visual>

### What makes up a ViewComponent?

ViewComponents are made up of four files, each with a specific function. Let's say you wanted to create a new ViewComponent called `Header`. The following 4 files would be created in a directory called `view/components/header/`. 

- `Header.js` - JavaScript logic for the ViewComponent's functionality
- `Header.dust` - Dust template markup where the layout logic is defined
- `Header.styl` - Stylus style sheets for all layout styles
- `Header.test.js` - JavaScript test using Chai assertions and Mocha.

<visual><break></break></visual>

### Header.js
```javascript
export default juno.ViewComponent.extend({
  is: 'view/components/header/Header'
});
```

This is the minimum JavaScript needed to render a ViewComponent in Juno. 
On line 2, the `is:` property is just the path to your component. We have adopted a standard naming convention of lowercase foldername and PascalCase component name. So if you were creating a component called ProductCard, the path would look like `view/components/productcard/ProductCard`. Also note that we're putting these components in a 'components' folder. This is totally optional, and you could, for example put it in another folder inside 'view/'. But wait, there's more... Your component doesn't even necessarily need to reside in 'view/'. As long as your component path matches where the component is located on the file system, Juno will render it. 

  <sub>_Note that we are using ES2015 syntax for all Juno experience code. For more info on how that is transpiled to ES5, see [babeljs.io](https://babeljs.io/)._</sub>

<visual><break></break></visual>

### Header.dust
```html
<div class="header">
  {@ViewComponent is="view/shell/components/globalnav/GlobalNav" /}
  <div class="banner">{data.bannerText}</div>
</div>
```

The Dust template must contain only one root node. Likewise, that root div class should match the correspdonding root class in your .styl file. In this example, our Header component contains 1 sub-component called GlobalNav and a basic HTML `<div>` tag for some banner text. Note that you can use standard HTML alongside your ViewComponent includes.

<visual><break></break></visual>

### Header.styl
```css
.header {
  width: 100%;
  font-size: 12px;
  color: blue;
}
```

The Stylus file is where you can style your component. If you are using NCSS in your project but require a custom override, this is the place to create that style.

<visual><break></break></visual>

### Header.test.js
The test file in each component is optional, but we recommend writing tests for all of your components in Juno. 
```javascript
import {expect} from 'chai';
import JunoTest from 'global/JunoTest';
import Header from 'view/components/header/Header';

describe('Header Component', () => {

  let data;
  beforeEach(() => {
    data = {
      data: {
        bannerText: 'banner text'
      }
    };
  });

  it('Should display banner text on page render', (done) => {
    let expectedBanner = "expectedBanner";
    data.data.bannerText = expectedBanner;
    getBannerText().then((text) => {
      expect(text).to.contain(expectedBanner, 'Expected value not found');
    }).finish(done);
  });

  function getBannerText() {
    return JunoTest.loadComponent(Banner, data).then(() => {
      return JunoTest.getDomText('.banner');
    });
  }
});
```
