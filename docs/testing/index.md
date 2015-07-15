---
title: Testing
template: doc.hbs
url: /docs/testing/
wip: true
---

# Testing

This application utilizes the following tools for testing. 
  
- [Mocha](http://mochajs.org/)
- [Chai](http://chaijs.com/api/bdd/)
- [Sinon](http://sinonjs.org/)
- [Burnside](http://stash.nikedev.com/projects/SQA/repos/burnside/browse)




#### File Structure
Tests reside in the same folder where the related .dust, .js and .less files reside.

``` bash
site
  └─┬ view
    ├─┬ layouts
    │ └─┬ components
    │   ├─┬ content
    │   │ ├── Content.dust
    │   │ ├── Content.js
    │   │ ├── Content.styl
    │   │ └── Content.test.js
    │   ├─┬ footer
    │   │ ├── Footer.dust
    │   │ ├── Footer.js
    │   │ ├── Footer.styl
    │   │ └── Footer.test.js
    │   └─┬ header
    │     ├── Header.dust
    │     ├── Header.js
    │     ├── Header.styl
    │     └── Header.test.js
    └─┬ shell
      ├── Shell.dust
      ├── Shell.js
      ├── Shell.styl
      └── Shell.test.js
```

### Writing Effective Tests

```javascript
import {expect} from 'chai';
```

Every test should start with `expect` and end in a function that does positive asseration.

- Starting with `expect` allows for good error messages in the case of null values.
- Ending in a function ensures that the test can only pass if the assertion passes.
- Positive assertion ensures that the value is precisely what is expected (rather than some unanticipated error value).
    
```javascript
expect(val).to.equal('doc');
```

Using mocha's testing framework, tests are set up with the following style:

```javascript
describe('Name of Component', () => {

    it('Should in depth describe the purpose of the test', () => {
        /**
         * Test code
        **/        
    });
    
    //Async Example
    it('Should in depth describe the purpose of the test', (done) => {
        /**
         * Test code
        **/  
        done(); // Mocha must know when the test is finished
    });
});
```

#### View Components
For tests of View Components we are testing the end functionality in piece on the dom.
In order to accomplish this we are using JunoTest which wraps the Nike QM module Burnside.

```javascript
import JunoTest from 'global/JunoTest';
```

##### Images
Many view components have images in the HTML.  For these components, be sure that you do not actually load http-based URLs as they will slow down your test run by doing an actual network call that the test doesn't care about yet still delays it.  Use the ["data:" protocol](https://developer.mozilla.org/en-US/docs/Web/HTTP/data_URIs) instead with bogus data.

##### Helpers
Available helper methods are located in `test/JunoTest.js`.

```javascript
/**
 * Initial setup of individual component
 * @param component: Reference to a ViewComponent
 * @param data: The set of key/value pairs to be passed to the component as published data.
 * @returns {*}
 */
loadComponent: function(component, data) 
  
/**
 * Retrieves the specific property from the dom of a given selector
 * @param selectorText: jQuery selector
 * @param propertyName  HTML property
 * @returns {*}
 */
queryDomProperty: function(selectorText, propertyName)

/**
 * Retrieves the text from the dom of a given selector
 * @param selectorText: jQuery selector
 * @returns {*}
 */    
queryDomText: function(selectorText) 

/**
 * Triggers an specified event on a given selector
 * @param selectorText: jQuery selector 
 * @param event: jQuery event 
 * @returns {*}
 */   
triggerEvent: function(selectorText, event) 

/**
 * Triggers a click event on the chosen selector
 * @param selectorText jQuery selector
 * @returns {*}
 */
click: function(selectorText)    

/**
 * Determines if an element is visible.
 * An element must not be considered Jquery hidden (display =none, etc..), 
 * opacity !== 0, and visibility !== 'hidden
 * to be considered visible.
 * @param selectorText
 */
isVisible: function(selectorText)

/**
 * Determines if class exist for a given selector
 * @param selectorText: jQuery selector
 * @param className: className whose existance is asserted
 * @returns {*}
 */    
hasClass: function(selectorText,className)
```

###### jQuery + Html Resources

-  [jQuery Selectors](https://api.jquery.com/category/selectors/)
-  [jQuery Events](https://api.jquery.com/category/events/)
-  [HTML Attributes](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes)


    
The typical format in testing a component 

```javascript
import {expect} from 'chai';
import JunoTest from 'global/JunoTest';
import ComponentName from 'path/to/component/ComponentName';


describe('ComponentName Type', () => {
  let data;
  beforeEach(() => {
    data = {};
  });

  it('should <expected behavior> when <expected precondition>', () => {
    return JunoTest.loadComponent(ComponentName,data).then(() => {
    
    });
  });
});
```

    
#### Services

How to handle testing Services shall come at a later date.


### Debug Tests

###### Check Isolated Component
Developers can check if a component loads the way that it was intended to by loading the component
itself. This can be done by typing the address for the testComponent in the browser. The data of 
component can be placed as well.

A developer can get the url for their component by running `gulp karma --testDebug`
Which will produce a log resembling the one below.

    /test/?componentPath=view/layouts/registration/shippay/ShipPay&componentData={"canSubmit":false,"translation":{"pageHeader":"pageHeader",
    "pageSubHeader":"pageSubHeader","lblLocation":"lblLocation","lblAddress":"lblAddress","lblApt":"lblApt","lblCity":"lblCity",
    "lblState":"lblState","lblZip":"lblZip","lblShippingPref":"lblShippingPref","lblCreditCard":"lblCreditCard",
    "lblExp":"lblExp","lblNext":"lblNext","lblSkipStep":"lblSkipStep"}}

###### Check the burnside module
Sometimes a response from the promise in a developers test might 
seem plain wrong and you would want to check where it is coming from. 
Open up `node_modules/burnside/index.js`. In the eventListener method, check
which case your test is using. After, you can use the sequence described in Check Isolated Component
and paste the case's jquery into the console. Then, you can verify or change things as need be. If this requires a
change to burnside, escalate the issue to a burnside developer or make the change in burnside yourself with burnside devs
on the pull request. `Must update burnside tests as well to reflect the change`

### Run Tests
Currently the server must already have been started with `juno serve`

Tests can be run by using the following commands:

```bash
gulp karma
```

Runs all the tests once

```bash
gulp karma:dev
```

Runs all the tests and then watches for any source or test file changes and re-runs the tests

For specifying which tests to run, using the following commands:

```bash
gulp karma --single <pattern>
gulp karma:dev --single <pattern>
```

The pattern will be used as a regex and run any test description matching that pattern

This regex can pick up the `describe` and `it` of a test suite:

```bash
gulp karma --single "Banner Component"
gulp karma --single "Should display body on page render"
```
