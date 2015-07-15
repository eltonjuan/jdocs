---
title: Fetching Data
template: doc.hbs
url: /docs/tutorial/step-03/
---
# Step 03 - Fetching Data

Now that we have our shell and routed component set up, we are ready to start building the functionality of our application. In this step of the tutorial, we are going to cover best practices for fetching data in Juno. Using a Service, we are going to write the foundation for fetching and persisting todo items in our application.

Reset your workspace to this step in the tutorial by running:

```bash
$ git checkout -f step-03
```

To view the diff. from the previous step, [click here.](http://stash.nikedev.com/projects/JUNO/repos/juno-tutorial/compare/diff?targetBranch=refs%2Fheads%2Fstep-02&sourceBranch=refs%2Fheads%2Fstep-03)

<visual><break></break></visual>

## Services

Services in Juno are meant to be re-usable components for fetching data. It is a very common pattern in building a modern web application to fetch data from an API before rendering your page. With Services, we can create modular, re-usable components for fetching, storing, and manipulating data from an API. 

For the sake of this tutorial, we are going to be using [Firebase](https://www.firebase.com) for storing and retrieving todo items from a data store. In this tutorial, we are only going to persist user data for the length of their session. 

Let's take a look at the Service that was created in this step: 

### Firebase.js
```javascript
export default juno.Service.extend({

  is: 'api/Firebase',
 
  base: 'https://juno-tutorial.firebaseio.com',

  fetchTodos() {
      // write logic to fetch list
      console.log('Fetching Todo List!');
  },

  updateTodos() {
      // write logic to update list
  }

});
```

If you've spent any time writing a ViewComponent in Juno, you'll notice that Services are structured in a similar fashion. The `is` property tells Juno where this component is located on our filesystem (relative to the site directory). It also provides a unique name for referencing the Service internally. Finally, the `base` property tells Juno what our URL base is for our API. It is used to build out the final URL when using the `fetch` and `fetchAndCache` methods that are available to Services

Now that we have our Service set up, we'll need to import it for use in our ViewComponent: 

### TodoList.js
```javascript
import firebase from 'api/Firebase';

export default juno.ViewComponent.extend({

  is: 'view/todo/components/todolist/TodoList',

  postRender() {
    console.log(firebase.fetchTodos());
  }

});
```

As you can see, we added an `import` statement at the top of our ViewComponent that has a reference to the Firebase Service that we saw above. This will allow us to reference our API in our ViewComponent. 

In addition, we've added a `console.log` statement to our `postRender()` method. This allows us to see that everything is wired up correctly. If all is well, you should see a log statement in your browser's console with the string 'Fetching Todo List!'.

Now that we have a mechanism for retrieving and persisting data, we can move on to [Step 4](/docs/tutorial/step-04/) where we'll see how the Services and ViewComponents work together.  




