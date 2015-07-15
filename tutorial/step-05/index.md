---
title: Removing Items and Managing State
template: doc.hbs
url: /docs/tutorial/step-05/
---
# Step 05 - Removing Items and Managing State

In the previous step, you learned how to add todo items, update the DOM, and persist the application state to Firebase. In this step, we're going to show you how to remove items, mark items as completed, and keep track of how many items are remaining. This is the final step in the tutorial. In the end, we'll have implemented nearly all of the features of the TodoMVC application using Juno.

Reset your workspace to this step in the tutorial by running:

```bash
$ git checkout -f step-05
```

To view the diff. from the previous step, [click here.](http://stash.nikedev.com/projects/JUNO/repos/juno-tutorial/compare/diff?targetBranch=refs%2Fheads%2Fstep-04&sourceBranch=refs%2Fheads%2Fstep-05)

<visual><break></break></visual>

## Removing Items

The code for doing removing items in our list is very similar to the code we wrote in the last step for adding an item. Let's take a look. 

### TodoList.js (lines 1-20)

```javascript
import firebase from 'api/Firebase';

export default juno.ViewComponent.extend({

  is: 'view/todo/components/todolist/TodoList',

  events: {
    'keydown input.new-todo': 'enterHandler',
    'click button.destroy': 'deleteHandler',
    'click input.toggle': 'toggleHandler',
    'click input.toggle-all': 'toggleAllHandler'
  },

  uid: undefined,

  defaults() {
    return {
      todos: []
    }
  }
})
```

In our events object, we added a new item for the delete button(line 9). Now, whenever the destroy button is called, we invoke the `deleteHandler` method as defined below: 

### TodoList.js (lines 59-66)

```javascript
/**
 * This method is invoked when the 'X' icon is clicked. It is used to remove a to-do from our list.
 * @param  {Object} e Event
 */
deleteHandler(e) {
  this.todos.splice(parseInt(e.provided), 1);
  this.sync();
},
```

The code in this method is pretty straightforward, except for the `e.provided` reference on line 62. Where does this value come from? Let's take a look at our Dust template: 

### TodoList.dust (lines 12-20)

```html
{#todos}
    <li class="{?completed}completed{/completed}" key="{title}">
        <div class="view">
            <input type="checkbox" class="toggle" data-provide="{$idx}" {?completed}checked{/completed}>
            <label>{title}</label>                  
            <button class="destroy" data-provide="{$idx}"></button>
        </div>
    </li>
{/todos}        
```

As you can see, we are creating a `<li>` element for each item in our `todos` array. When the destroy button is clicked, we need to know which item in the `todos` array it represents. To do this, Juno provides a handy feature called provide. 

On line 17, we have the data attribute `data-provide`. Behind the scenes, Juno will look for this attribute and automatically place its value into our Event object with the key of `provided`. This is a quick and easy way to help us so that we don't have to do an additional query on the DOM to get the index of the current item being clicked. In our JavaScript (as seen above), we can just reference `Event.provided`. 

So, using the current index of the item being clicked, we splice the `todos` array, and call `sync` to trigger a DOM update and sync our application state to Firebase, and voila! We can now remove items from our todo list.

<visual><break></break></visual>

## Marking Items as Completed

Another critical feature to managing any todo list is marking items as complete! Let's take a look at the code we use to do that.

Again, we've added a new item to our `events` object (line 10), which will invoke the `toggleHandler` method when the checkbox is clicked. 

### TodoList.js (lines 67-74)

```javascript
/**
 * This method is invoked when the 'completed' toggle is clicked. It will toggle the completed state
 * @param  {Object} e Event
 */
toggleHandler(e) {
  this.todos[e.provided].completed = e.target.checked;
  this.sync();
},
```

This is all the code it takes to mark an item as completed. Again, we are using the `provided` value on the Event to get the current index of the item being clicked. We then set it's `completed` value. And we sync the new state to Firebase. 

The interesting part here is how we leverage the state of our component's data to affect our template. 

### TodoList.dust (lines 12-20)

```html
{#todos}
    <li class="{?completed}completed{/completed}" key="{title}">
        <div class="view">
            <input type="checkbox" class="toggle" data-provide="{$idx}" {?completed}checked{/completed}>
            <label>{title}</label>                 
            <button class="destroy" data-provide="{$idx}"></button>
        </div>
    </li>
{/todos}        
```

If you look on the second line in the sample above, you'll see that we use an inline Dust conditional `{?completed}` to surround a class name. If the value of completed is `true`, the class will be applied. If not, the class is ommitted. This is an extremely powerful concept of the Juno framework. 

No more managing the state of your UI using JavaScript. Just update the data model when something changes, call a `set()` or `update()`, and allow the re-render of the template to add or remove classes for you.

No more `addClass`, `removeClass`, `toggleClass`, etc. No more jQuery spaghetti code! This nifty little trick can be used to show/hide elements, trigger CSS animations, and much more! 

Anytime you go to reach for your old pal jQuery, you should be asking yourself, how can I accomplish the same task through my data model?

<visual><break></break></visual>

## Displaying Remaining Items

The final feature that we implemented in this step is the ability to show the remaining items (completed === false) in the footer of the todo list. 

### TodoList.js (lines 88-97)

```javascript
/**
 + This method will return the length of the remaining items for us to-do. It is invoked directly from
 + our dust template.
 + @return {Number} The number of remaining to-dos. 
 */
remaining() {
  return this.todos.filter((item) => {
    return (item.completed === false);
  }).length;
},
```

This is all the code it takes to show the number of remaining items in our application view. We create a function that iterates over the array of todos and filter them down to items that aren't completed and return the length of that array. 

Dust allows us to reference functions inline in our template:

### TodoList.dust (lines 23-28)

```html
{?todos}
    <footer class="footer">
        <span class="todo-count">{remaining} items left</span>
        <button class="clear-completed"></button>
    </footer>
{/todos}
```

As you can see above, we just reference the `remaining` function. Dust will invoke that function, and the value that it returns will be placed into our template. 

We then rely on DOM re-renders to update the remaining value. When items are added, removed, or marked as completed, a DOM re-render occurs, which will invoke the `remaining` method and return an updated value. Pretty neat!

<visual><break></break></visual>

## That's it!

We have now implemented most of the major features of the TodoMVC application in Juno! We're missing a few things like filters and editing items, but why not try to implement those yourself? 

Continue on to our [Final Words](/docs/tutorial/step-06) section for a summary of what we covered and the next steps for learning more advanced topics in Juno. 








