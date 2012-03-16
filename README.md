# Getting Started with Ember.js

*This is an adaptation of SproutCore 2.0 Getting Started Guide*

Works for >= Ember.js 0.9.5 version.

You can see the finished source code on this Github repo.

## 1. Installing Ember

Download the [Ember Starter Kit](https://github.com/emberjs/starter-kit/downloads)
if you haven’t already. Unzip and it open the directory in your favorite text
editor. *In this repo, i'm using my [starter kit version](https://github.com/frodsan/emberjs-starter-kit/downloads)
that uses jQuery's current version (1.7.1).*

## 2. Core Concepts

*Source: [Ember.js Website](http://emberjs.com/)*

These are the three features that make Ember a joy to use:

* Bindings
* Computed properties
* Auto-updating templates

**Bindings:**

Use bindings to keep properties between two different objects in sync. You just declare a binding once, and Ember will make sure changes get propagated in either direction.

```javascript
MyApp.president = Ember.Object.create({
  name: "Barack Obama"
});
     
MyApp.country = Ember.Object.create({
  // Ending a property with 'Binding' tells Ember to
  // create a binding to the presidentName property.
  presidentNameBinding: 'MyApp.president.name'
});

// Later, after Ember has resolved bindings...     
MyApp.country.get('presidentName');
// "Barack Obama"
```

**Computed Properties:**

Computed properties allow you to treat a function like a property:

```javascript
MyApp.president = Ember.Object.create({
  firstName: "Barack",
  lastName: "Obama",
     
  fullName: function() {
    return this.get('firstName') + ' ' + this.get('lastName');
       
  // Call this flag to mark the function as a property
  }.property()
});
     
MyApp.president.get('fullName');
// "Barack Obama"
```

Computed properties are useful because they can work with bindings, just like any other property.

Many computed properties have dependencies on other properties. For example, in the above example, the fullName property depends on *firstName* and *lastName* to determine its value. You can tell Ember about these dependencies like this:

```javascript
MyApp.president = Ember.Object.create({
  firstName: "Barack",
  lastName: "Obama",
     
  fullName: function() {
    return this.get('firstName') + ' ' + this.get('lastName');
     
  // Tell Ember that this computed property depends on firstName
  // and lastName
  }.property('firstName', 'lastName')
});
```

Make sure you list these dependencies so Ember knows when to update bindings that connect to a computed property.

**Auto-updating templates:** 

Ember uses [Handlebars](https://github.com/wycats/handlebars.js/), a semantic templating library. To take data from your JavaScript application and put it into the DOM, create a script tag and put it into your HTML, wherever you'd like the value to appear:

```html
<script type="text/x-handlebars">
  The President of the United States is {{MyApp.president.fullName}}.
</script>
```

Here's the best part: templates are bindings-aware. That means that if you ever change the value of the property that you told us to display, we'll update it for you automatically. And because you've specified dependencies, changes to those properties are reflected as well.

## 3. Create the Namespace

Open the JavaScript file located at `js/app.js`. Replace the default contents with the following:

```javascript
var Todos = Ember.Application.create();
```

This code creates a namespace for your application (called Todos), which is also an instance of Ember.Application. **It is important that every Ember app creates an instance of Ember.Application, because it is responsible for routing browser events to your views.**

## 4. Defining Your Model

In this tutorial, we want to create a list for managing todos. Users should be able to create a new todo with a specific task, then check it off once it’s done.

Let’s define our model as a new subclass of Ember.Object in the `js/app.js` file:

```javascript
Todos.Todo = Ember.Object.extend({
  title: null,
  isDone: false
});
```

*Make sure you insert the new code after the Todos object is created.*

We’ve now defined a class with two properties: `title`, a String, and `isDone`, a Boolean.

## 5. Managing the Model Using a Controller

Now that we know what our data looks like, let’s create a controller to manage it. Since we want to maintain an ordered list of todos, we’ll use an instance of `Ember.ArrayController`.

```javascript
Todos.todosController = Ember.ArrayController.create({
  // Initialize the array controller with an empty array.
  content: []
});
```

**Note: ** In MVC frameworks, like Ember, the controller layer bridges the model layer, which is only concerned with a pure-data representation of objects, and the view layer, which is only concerned with representing those objects.

Now we have an array controller with no content. Let’s add a method to create a new todo:

```javascript
// updating previous code
 
Todos.todosController = Ember.ArrayController.create({
  // Initialize the array controller with an empty array
  content: [],

  // Creates a new todo with the passed title then adds it
  // to the array
  createTodo: function(title) {
    var todo = Todos.Todo.create({ title: title });
    this.pushObject(todo);
  }
});
```

`Ember.ArrayController` acts as a proxy onto its content Array. Ember will propagate any modifications made to the ArrayController to the content Array.

## 6. Doing It with Style

We’ve provided a simple stylesheet to give the application some style. Download the [CSS file](https://raw.github.com/frodsan/emberjs-getting-started/master/css/todos.css), add it to your project’s `css` directory, then include it in your `index.html` `<head>` tag:

```html
<link rel="stylesheet" href="css/todos.css">
```

## 7. Creating New Todos with a Text Field

We’ve got our model and controller set up, so let’s move on to the fun part: creating the interface for our users. The first step is to create a text field into which the user types the name of their todo. Ember uses Handlebars templates to quickly define the application’s interface. While Handlebars makes it easy to markup HTML quickly, you’ll see that it also has been extended to automatically take advantage of Ember's bindings.

To start building our views, let’s open `index.html`.

First, add an `<h1>` tag with the name of the application:

```html
<h1>Todos</h1>
```

You’ll also see that the starter kit includes a default Handlebars template. Remove it and replace it with the following Handlebars template, which emits a text field for our users to type in:

```html
<script type="text/x-handlebars">
  {{view Ember.TextField id="new-todo" placeholder="What needs to be done?"}}
</script>
```

**Make sure you remove the stub template in index.html that is included in the starter kit.**

Here we’ve specified a text field, with a unique id attribute (so it can be styled via CSS), as well as a placeholder attribute that will be displayed in modern HTML5 browsers.

**For more information about using Handlebars, visit the [Handlebars website](http://www.handlebarsjs.com/). To learn more about using Handlebars with Ember, make sure to check out the [Using Handlebars Templates guide](http://guides.sproutcore20.com/using_handlebars.html).**

Now that we’ve got model, view, and controller represented, it’s time to open the app in our browser and see how it looks.

Double click on your `index.html` file to open it in your browser. You should see your application load. Once you’ve verified that the application is up and running, it’s time to tell Ember how to handle events for your `<input>` tag. When the user types in the field and presses the return key, we will create a new Todo and have it inserted into the content of the array controller.

**In Ember, view objects are responsible for updating the DOM and handling events. Among other things, this allows us to buffer changes to the DOM for maximum performance and to support generic cross-platform event handling. Whenever you want to display dynamic content or handle events, you will use a view object.**

Insert this code in `js/app.js` file:

```javascript
Todos.CreateTodoView = Ember.TextField.extend({
  insertNewline: function() {
    var value = this.get('value');
 
    if (value) {
      Todos.todosController.createTodo(value);
      this.set('value', '');
    }
  }
});
```

Since `CreateTodoView` will handle events for a text field, we create a subclass of Ember.TextField, which provides several conveniences for working with these input controls. For example, you can access the `value` property and respond to higher level events, such as `insertNewline`, when the user presses enter.

Now that we have defined our view, let’s update the template to use our new view subclass. Switch back to `index.html` and update the view helper to say `Todos.CreateTodoView` instead of `Ember.TextField`.

```html
<h1>Todos</h1>

<script type="text/x-handlebars">
  {{view Todos.CreateTodoView id="new-todo" 
    placeholder="What needs to be done?"}}
</script>
```

Now that we have UI to create new todos, let’s create the code to display them. We’ll use the Handlebars `#collection` helper to display a list of items. `#collection` will create an instance of Ember.CollectionView that renders every item in its underlying Array using the enclosed HTML.

```html
<script type="text/x-handlebars">
  {{view Todos.CreateTodoView id="new-todo" 
    placeholder="What needs to be done?"}}
    
  {{#collection contentBinding="Todos.todosController" tagName="ul"}}
    {{content.title}}
  {{/collection}}
</script>
```

Notice that we’ve also told the collection to bind its `content` property to our todosController. For every item in the array controller, the collection view will create a new child view that renders the `{{content.title}}` template.

You set up bindings by creating a property whose name ends in Binding. In this case, we bind Todos.todosController to the collection view’s content property. When one end of a binding changes, Ember will automatically update the other end.

This is a good time to open `index.html` again (or reload if you still have it loaded in your browser). It should look the same as before. Type a todo into the text field and hit return.

Look at that! As soon as we create a new todo and insert it into the array controller, the view updates automatically.

You’ve now seen a little bit of the power of Ember. By using Ember’s bindings to describe the relationship between your data and your views, you were able to change the data layer and let Ember do the hard work of updating the view layer for you.

This is actually a core concept in Ember, not just something that demos well. Ember’s binding system is designed with the view system in mind, which makes it easy to work directly with your data and not need to worry about manually keeping your view layer in sync. You will see this concept over and over again in the rest of this tutorial and in other guides.

## 8. Getting Things Done

We now have the ability to add todos, but no way to mark them as done. Before the frustration of a never-ending todo list gets the better of us, let’s add the ability to mark todos complete.

The first thing we need to do is add a checkbox to each todo list itEmber. As was mentioned earlier, if we want to handle events, such as user input, we need a view. In this case, we are adding a checkbox and want to be notified whenever the value of the checkbox is changed by the user. Let’s update the Handlebars template in `index.html` to look like the following:

```html
<script type="text/x-handlebars">
{{view Todos.CreateTodoView id="new-todo" 
  placeholder="What needs to be done?"}}
 
{{#collection contentBinding="Todos.todosController" tagName="ul"}}
  {{view Ember.Checkbox titleBinding="content.title"
      valueBinding="content.isDone"}}
{{/collection}}
</script>
```
Let’s take a second to talk about the bindings we just set up. For every item in its underlying array, `Ember.CollectionView` will create a new item view whose `content` property contains the object the view should represent. In our case, there will be a child view for each todo. Since the checkbox is a child view of each item view, when we bind to `content.title`, we’re saying to bind to the `title` property of the Todo object represented by the item view.

Under the hood, Ember binds an event handler to the change event of the checkbox and updates value when the event occurs. This may change as needed for browser compatibility, but working with a Ember property insulates you from those concerns.

Before you reload the browser to see the new checkbox todo list items, the stylesheet we provided includes a CSS class meant to give completed todos a unique look. Let’s bind the class of each item to the object’s `isDone` property.

We’ll use a property on the collection helper to set up this binding:

```html
{{#collection contentBinding="Todos.todosController" tagName="ul"
  itemClassBinding="content.isDone"}}
  {{view Èm.Checkbox titleBinding="content.title"
    valueBinding="content.isDone"}}
{{/collection}}
```

This property sets up a binding on each of the item views. Each item will get the class is-done if its associated content object’s `isDone` property is true. Ember will automatically dasherize property names into class names.

**All views have a number of properties, including id, class, and classBinding. The collection helper allows you to prefix any of those properties with item, which will then apply to each of the child item views. For instance, if you use the itemClass property on a collection, each item will get that class name.**

Now reload your application in the browser and try it out. As soon as you click a todo’s checkbox, the text will become crossed out. Keep in mind you didn’t need to update any views when marking the Todo as done; bindings did it for you. If a different part of the app changes the Todo item’s isDone property, your list item would automatically update without any more work on your part.

## 9. The More You Know

We can now create todos and mark them as being complete. While 76% of all statistics are made up, let’s see if we can display more accurate information from the data we have. At the top of our list, let’s display the number of todos remaining. Open `index.html` and insert this new view:

```html
<!-- Insert this after the CreateTodoView and before the collection. -->
{{#view Todos.StatsView id="stats"}}
  {{remainingString}} remaining
{{/view}}
```

Handlebars expressions, like `{{remainingString}}`, allows us to automatically update the DOM when a property on our view changes. In this case, we’re saying that Todos.StatsView should always display the most up-to-date value to the view’s `remainingString` property. As with the other bindings, this will be updated for us automatically whenever the value changes.

Let’s go ahead and implement that view in `app.js` now:

```javascript
Todos.StatsView = Ember.View.extend({
  remainingBinding: 'Todos.todosController.remaining',
 
  remainingString: function() {
    var remaining = this.get('remaining');
    return remaining + (remaining === 1 ? " item" : " items");
  }.property('remaining')
});
```

`remainingString` contains a pluralized string, based on the number of remaining todos. Here is another core piece of Ember in action, this one called a computed property. Computed properties are properties whose value is determined by running a function. For example, if `remaining` equals 1, `remainingString` will contain the string "1 item".

We say that `remainingString` **depends on** `remaining` because it requires another value to generate its own value. We list these **dependent keys** inside the `property()` declaration.

We’ve also bound the view’s `remaining` property to the todosController‘s `remaining property`. This means that, because of the dependency chain we’ve described, if Todos.todosController.remaining changes, remainingString will automatically update.

When we have information that views need but is based on aggregate information about our models, the array controller is a good place to put it. Let’s add a new computed property to `todosController` in `app.js`:


```javascript
// updating previous code
 
Todos.todosController = Ember.ArrayController.create({
 
  // ...
  createTodo: function(title) {
    var todo = Todos.Todo.create({ title: title });
    this.pushObject(todo);
  },
 
  remaining: function() {
    return this.filterProperty('isDone', false).get('length');
  }.property('@each.isDone')
});
```

Here, we specify our dependent key using `@each`. This allows us to depend on properties of the array’s items. In this case, we want to update the remaining property any time `isDone` changes on a Todo.

**The @each property also updates when an item is added or removed.**

It’s important to declare dependent keys because Ember uses this information to know when to update bindings. In our case, our StatsView updates any time todosController’s remaining property changes.

As we build up our application, we create **declarative** links between objects. These links describe how application state flows from the model layer to the HTML in the presentation layer.

## 10. Clearing Completed Todos

As we populate our list with todos, we may want to periodically clear out those we’ve completed. As you have learned, we will want to make that change to the todosController, and let Ember automatically propagate those changes to the DOM.

Let’s add a new `clearCompletedTodos` method to `todosController`.

```javascript
// updating existing code
 
Todos.todosController = Ember.ArrayController.create({
 
  // ...
  remaining: function() {
    return this.filterProperty('isDone', false).get('length');
  }.property('@each.isDone'),
 
  clearCompletedTodos: function() {
    this.filterProperty('isDone', true).forEach(this.removeObject, this);
  }
});
```
Next, let’s add a button to our template. Open `index.html` and add a button inside the HTML for `StatsView` as shown here:

```html
{{#view Todos.StatsView id="stats"}}
  {{#view Ember.Button classBinding="isActive"
    target="Todos.todosController"
    action="clearCompletedTodos"}}
    Clear Completed Todos
  {{/view}}
  {{remainingString}} remaining.
{{/view}}
```

We’ve defined an instance of Ember.Button, which calls a method on an object when it is clicked. In this case we’ve told the button to call the `clearCompletedTodos` method (its action) on the `Todos.todosController` object (its target).

We’ve also told it to add an `is-active class` to the view when it is clicked or tapped. Every Ember.Button has an `isActive` property that will be true when it is in the process of being clicked. This allows us to display a visual cue to the user that they have hit the right target.

Go back to your browser and try it out. Add some todos, then mark them done and clear thEmber. Because we previously bound the visual list to the `todosController`, making a change through new means has the expected effect.

## 11. Marking All as Done

Let’s say you’ve decided to actually get all of your work done. Wouldn’t it be nice to have a way to easily mark every todo as complete?

It turns out that, due to our application’s declarative nature, all the hard work is already done. We just need a way to mark every Todo complete.

Let’s first create a new computed property on our controller that describes whether or not every todo is done. It might look something like this:

```javascript
Todos.todosController = Ember.ArrayController.create({
 
  // ...
  
  clearCompletedTodos: function() {
    this.filterProperty('isDone', true).forEach(this.removeObject, this);
  },

  allAreDone: function() {
    return this.get('length') && this.everyProperty('isDone', true);
  }.property('@each.isDone')
});
```

**Ember has many enumerable helpers. everyProperty(‘isDone’, true) returns true if every item in the array has an isDone property that evaluates to true. You can find out more in the [Enumerables guide](http://guides.sproutcore20.com/enumerables.html).**

Next, open `index.html`, we’ll create a checkbox view to mark all items complete and bind its value to the controller’s property:

```html
<!-- directly below the Todos.StatsView -->
 
{{view Ember.Checkbox class="mark-all-done"
  title="Mark All as Done"
  valueBinding="Todos.todosController.allAreDone"}}
```

If you were to reload your browser and use the app now, you’d notice that clicking all of the checkboxes of your todos will cause the “Mark All as Done” checkbox to become checked. However, it doesn’t work in the other direction: clicking “Mark All as Done” has no effect.

So far, our computed properties have described how to calculate a value from dependent properties. However, in this case, we want to accept a new value, and update dependent properties to reflect it. Lets update our `allAreDone` computed property to also accept a value.

```javascript
// updating existing code
 
Todos.todosController = Ember.ArrayController.create({
 
  // ...
 
  allAreDone: function(key, value) {
    if (value !== undefined) {
      this.setEach('isDone', value);
 
      return value;
    } else {
      return !!this.get('length') && this.everyProperty('isDone', true);
    }
  }.property('@each.isDone')
});
```

When you set a computed property, your computed property function is called with the property key as the first parameter and the new value as the second. To determine if Ember is asking for the value or trying to set it, we examine the `value parameter`. If it is defined, we iterate through each Todo and set its `isDone` property to the new value.

Because bindings are bi-directional, Ember will set `allAreDone` to true when the user clicks the “Mark All as Done” checkbox. Conversely, unchecking it will make Ember set `allAreDone` to false, unchecking all todos.

Reload the app and add some todos. Click “Mark All as Done”. Wow! Each of the individual check boxes gets checked off. If you uncheck one of them, the “Mark All as Done” checkbox un-checks itself.

**When you use Ember, as you scale your UI, you never need to wonder whether a new feature will work consistently with parts of the UI you already implemented. Since you build your view layer to simply reflect the state of your models, you can make changes however you want and see them update automatically.**

## Resources:

* Documentation: <http://emberjs.com/>
* Github Repository: <https://github.com/emberjs/ember.js>
* Wiki: <https://github.com/emberjs/ember.js/wiki>

Thanks,
[@frodsan](https://twitter.com/frodsan)
