---
title: Adding Items
template: doc.hbs
url: /docs/tutorial/step-04/
---
# Step 04 - Adding Items

In the previous step, we learned how to create a Service and imported it in for use in our ViewComponent. In this step, we're going to cover how you add items to the todo list, update the DOM, and persist state to Firebase. 

Reset your workspace to this step in the tutorial by running:

```bash
$ git checkout -f step-04
```

To view the diff. from the previous step, [click here.](http://stash.nikedev.com/projects/JUNO/repos/juno-tutorial/compare/diff?targetBranch=refs%2Fheads%2Fstep-03&sourceBranch=refs%2Fheads%2Fstep-04)

**Note:** You'll need to run an `npm install` after checking out this step. 

<visual><break></break></visual>

## Vendor CSS

It may appear that a lot has changed since the previous step, but we really didn't add a lot of new code. Let's take a look at the changes we made. 

The creators of the TodoMVC sample application have made styling easy for us by providing a [node module](https://www.npmjs.com/package/todomvc-app-css) containing the necessary styles for building out the TodoMVC application. How do we get this CSS into our site? 

### .config/gulp.js
```javascript
/*
 * gulp.js
 * =======
 * 
 * This configuration is used to change the build process of Juno.
 */

module.exports = {
  extras: false,
  vendor: {
    bundles: {
      css: [
        'node_modules/todomvc-app-css/index.css'
      ]
    }
  }
}
```

As part of the Juno CLI scaffold, we have provided a config. file that allows you to specify CSS/JS vendor scripts that will get bundled into the vendor scripts for our site. In this case, we supply a path to the TodoMVC CSS in the `node_modules` directory. When the `juno serve` command is executed, this file will be automatically concat'd into our `vendor.min.css` bundle. 

<visual><break></break></visual>

## Fetching and Persisting Data

In the previous step, we wired up our Service to be consumed by our ViewComponent. In this step, we've added the necessary functionality for retrieving and persisting data to Firebase. 

### Firebase.js
```javascript
export default juno.Service.extend({

  is: 'api/Firebase',
 
  base: 'https://juno-tutorial.firebaseIO.com/users/',

  /**
   * A method to fetch a to-do list for a given user.
   * @param  {String} uid The user ID whose list you want to fetch
   * @return {Object} request A request Promise that is chainable 
   */
  fetchTodos(uid) {
    return this.fetch(`${uid}/.json`).then(res => res.json());
  },
  /**
   * A method that will persist a user's to-do list
   * @param  {String} uid   The user ID whose list you want to persist.
   * @param  {Array} todos  The to-do list array
   * @return {Object}       The $.ajax object so we can .then
   */
  updateTodos(uid, todos){
    let url = `${this.base}${uid}/.json`;
    let conf = {
      body: JSON.stringify(todos),
      method: 'put',
      headers: {
          'Accept': 'application/json',
          'Content-Type': 'application/json'
      },
    };
    return this.fetch(url, conf).then(res => res.json());
  }
});
```

In the `fetchTodos` method, we call `this.fetch`, a method available to Services that handles requests and returns a promise. `fetch` is an upcoming web standard. You can read more about it [here](https://fetch.spec.whatwg.org/) as well as on [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API).

<visual><break></break></visual>

## ViewComponent Logic

The majority of our application logic will exist inside of the `TodoList.js` ViewComponent file. We'll break it down and go over each section below: 

### TodoList.js (lines 1-17)

```javascript
import firebase from 'api/Firebase';

export default juno.ViewComponent.extend({
  
  is: 'view/todo/components/todolist/TodoList',

  events: {
    'keydown input.new-todo': 'enterHandler'
  },

  defaults() {
    return {
      todos: []
    }
  },

  uid: undefined,

})
```

Everything in this block should look very familiar to you with the exception of the `events` object and `defaults` function. 

The `events` object in a ViewComponent is used to define event listeners and handlers when a given event occurs. In this case, the key is an event name followed by a selector representing the element you want to listen for events on. The value is the name of the handler function. 

In this case, when we detect a `keydown` event on the `input` element, we want to invoke the `enterHandler` method. For more information on events, see the [Events](/docs/view-component-api#events-object-) documentation. 

The `defaults` function is a concept specific to Juno. These are properties that belong specifically to every instance of a ViewComponent. For now, just think about it as we want the default value of our `todos` array to be empty.

### TodoList.js (lines 19-35)

```javascript
create(req, res) {
  let uid = req.cookies.get('uid');
  if (!uid) {
    // We don't have a user, let's create a new one
    let newId = Math.random().toString(16).slice(2);
    this.uid = newId;
    res.cookie('uid', newId, {httpOnly: false});
    this.render();
  } else {
    // We have a user, so let's fetch their to-do list
    this.uid = uid;
    firebase.fetchTodos(uid).then((data) => {
      this.set('todos', data || []);
      this.render();
    });
  }
},
```

In this block, we are tapping into the `create` method of the [component lifecycle](/docs/component-lifecycle/) in order to execute some logic on the server, before we deliver the page to the user. 

By default, Juno provides the `req` and `res` objects (from [Express](http://expressjs.com/api.html#req)) to the `create` method for every ViewComponent. This allows us to examine key information about a user's request to our site. 

In the first case (we don't have a user), we generate a pseudo-random ID, set it on our component instance, set a cookie containing the ID on the response object (which will be delivered to the user), and finally we call `this.render()`, telling Juno that we are ready to render our component. 

Anytime you override the `create` method on a ViewComponent, you **must** call `render()`, or else you will block the site from rendering. In `__DEV__`, Juno will throw a helpful warning if you forget to call `render()` in a ViewComponent definition. 

In the second case (we have a returning user), we set the user's ID on our component instance, then we call the `fetchTodos` method on our Service. When that request resolves out, we set `todos` on our component instance, and then we make a call to `render()`, letting Juno know that our component is ready to render.

### TodoList.js (lines 38-60)

```javascript
/**
 * This is the method that is invoked when you type a new to-do and press the "enter" key. 
 * @param  {Object} e The Event object
 */
enterHandler(e) {
  // If anything other than the enter key is pressed, just return out. 
  if (e.keyCode !== 13){
    return;
  }
  let newTodo = {
    title: e.target.value,
    completed: false
  };
  e.target.value = '';
  this.todos.push(newTodo);
  this.sync();
},

/**
 * This method will sync our app. state with firebase.
 */
sync() {
  this.update();
  firebase.updateTodos(this.uid, this.todos);
}
```

In this block, we have the `enterHandler` method as mentioned above in our events object, as well as the `sync` method that will trigger a view update and persist our data to our Firebase backend. 

In the `enterHandler` method, our logic is pretty straightforward. If anything other than the enter key is pressed, we just return out. If the enter key was pressed, we create a `newTodo` object with the text from the input field and then clear out the input text. 

We then push the new todo to our `todos` array and then call the `sync()` method. This makes a call to `update()` which will trigger a re-render of the DOM.

Alternatively, you could have called `this.set(todos)`, which sets the updated `todos` array onto the component and triggers a DOM update.

Protip: The `.set` method will always trigger a re-render of the DOM. If you don't care about triggering a re-render, just work with your data directly (don't use set).

Finally, we call the `updateTodos` method in our Service. This will save the updated list to Firebase, and we are good to go!

You'll also see some changes occurred in the `TodoList.dust` template file. We're not going to go into details about the changes, as we're using pretty basic Dust functionality. For a Dust tutorial, [check out their docs!](http://www.dustjs.com).

Now that we have the ability to add items, we will continue to develop the rest of the functionality of the app. In the [next step](/docs/tutorial/step-05/), we will be adding the logic to delete items, mark items as completed, and display the number of remaining items.

