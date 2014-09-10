Hey, I know I'm not perfect. I've tried to write my own answers to each of these
questions but I know some things will still remain uncertain. In that case, I
try to include a couple great resources to learning the theory / motivation
behind each of the questions to help all of us understand these questions
better!

# JS Questions

## Explain event delegation

Event delegation arose from the use of event listeners in the DOM. If you're
unfamiliar with event listeners, these are just events that JavaScript
developers are able to listen to on html nodes. 

A classic example:

```html
<div class="foo"></div>

<script>
  document.querySelectorAll('.foo').addEventListener('click', function() {
    console.log('You clicked me!');
  });

  // Or in jQuery
  $('.foo').on('click', function() {
    console.log('You clicked me!');
  });
</script>
```

So in the example above we're just adding a `click` event handler which just
listens for a user click on the div we've created and will then fire the
function each time the event registers.

This pattern is just fine for single elements, but imagine having a list of
items and you want to add a click event listener to each one? Instead of having
one event listener we now have 5, 10, 50, etc... This number could end up being
really huge if we have a lot of list items, or a lot of nodes in general that we
want to listen to a specific event for, in this case it would be click.

So, just to recap real quick we might have the following situation:

```html
<ul>
  <li>1</li>
  <li>2</li>
  <li>3</li>
  <li>4</li>
  <li>5</li>
  <!-- ... -->
</ul>

<script>
  // Attach an event listener to each of the list items we have
  $('li').each(function() {
    $(this).on('click', function() {
      console.log('added an event listener!');
    });
  });
</script>
```

So in this case we have a list and are attaching an event handler to each one.
Since we only have 5 items, it's not too bad. Imagine if we have more though.
We'd attach an event listener on each one and each one would be listening for
that event on that specific list item. As this number grows larger and larger,
our performance starts to take a hit. How do we solve this issue? Easy! Just use
*event delegation*.

Event delegation solves this problem by allowing us to attach an event listener
to a parent node and any event that happens in this element "bubbles up". This
process of bubbling up allows you to hook into the parent nodes event listener
and also gives you access to the event *target*. Here's what I mean:

```html
<ul>
  <li>1</li>
  <li>2</li>
  <li>3</li>
  <li>4</li>
  <li>5</li>
  <!-- ... -->
</ul>

<script>
  $('ul').on('click', function(event) {
    console.log($(event.target).html()); // Logs out 1, 2, 3, 4, 5 depending on what
    li is clicked!
  });
</script>
```

So now when we click on an li element, this event bubbles up to our parent ul.
This unordered list has an event listener attached to it and this will now
trigger. When this event listener triggers it executes our anonymous function
which has access to an `event` parameter. This event parameter has a `target`
property which actually tells us what node was clicked.

And that's basically it! That's a brief review of event delegation.

## Explain how `this` works in JavaScript

There are five ways the value of `this` gets set in JavaScript.

1. Global

If you just call `this` in a JavaScript context it will refer to the global
object. This will be `window` if you are working in the browser or `process` if
you are in node. 

**Note:** if you are using `'use strict';` mode then this will be `undefined`

2. Function invocation

When calling a function on an object, `this` is set to the object making the
call. For example:

```js
var foo = {
  foobar: "JS rules!",
  bar: function() {
    console.log(this.foobar);
  }
};

foo.bar(); // "JS Rules!"
```

3. Function Declaration

Pretty natural way of thinking about `this`. Say you have a function:

```js
function foo(bar) {
  this.member = bar;
};

var foobar = new foo("bar");
foobar.member; // "bar"
```

4. Apply / call / bind

You can give a function its value of this by using the functions `apply`,
`call`, or `bind`.

5. Fat arrow

## Explain how prototypal inheritance works in JavaScript

## How do you go about testing your JavaScript?

There are plenty of options to test your JavaScript in 2014. I think the
appropriate response is to ask about what kind of testing they are looking for.
Writing tests just to write test may not be worth it until you are certain the
implementation you are working on is the one you are sticking with. At that
point you should ask yourself what kind of bugs is QA reporting and choose the
appropriate testing level to correspond with this.

Some awesome testing frameworks include Mocha, Jasmine and their corresponding
tools chai, ....

And using this you can do unit tests, integration tests, etc.

## AMD vs. CommonJS

As of this writing, it seems that CommonJS is the better choice. It has a less
frustrating syntax for developers to get into and also provides a better range
of features compared to AMD. While AMD asynchronously loads modules, CommonJS
loads them synchronously (although there are ways to not do this). Both actually
end up leading to large JavaScript files that you will probably concatenate and
minify and then serve to clients.

A smarter approach might be to talk about webpack and how it encourages a
dependency graph, allowing you to only the static content which your users need
when accessing a part of your site. This makes a lot of sense when dealing with
SPA's.

## Explain why the following doesn't work as an IIFE: `function foo() { }();

This doesn't work because it is actually invalid syntax. In order to evaluate
something in JavaScript it is true that we do put `()`'s after it. However, in
this situation we have a function declaration which is not surrounded by
parenthesis and this causes a syntax error. Since the JavaScript compiler has to
evaluate a function before it can execute it you need to put parenthesis around
the function in order to turn it into an IIFE.

## What's the difference between a variable that is `null`, `undefined`, or
`undeclared`?

TODO: variable declaration and definition? Need to remember the correct terms

In JavaScript, you can think of `undefined` as meaning that a variable has been
declared (think `var foo;`) but it hasn't been assigned a value. 

`null` is an assignment value and is a representation of no value for a
variable.

`undeclared` just means that the variable hasn't been declared at any point in
your program. 

Sources:

[Stack Overflow](http://stackoverflow.com/questions/5076944/what-is-the-difference-between-null-and-undefined-in-javascript)

## What is a closure, and how/why would you use one?

Closures are a technique in JavaScript of providing encapsulation to our objects
and functions.

Simple example:

```javascript
function addSecretSalt() {
  var secret = "JS Rules!";

  return function(salt) {
    return secret + salt;
  }
};

// Usage
var secretAndSalt = addSecretSalt('salted'); // "JS Rules!salted"
```

At no point does anything outside of `addSecretSalt()` has access to the secret
variable. 

## What's a typical use case for anonymous functions?

There are a ton of use cases for anonymous functions. Here are some good ones:

### Callbacks in `node`.

```javascript
var fs = require('fs');

fs.readFileAsync('foo.txt', function(buffer) {
  var fileContents = buffer.toString();
  // do stuff!
});

```

### Callbacks for event listeners

```javascript
// Using jQuery...
$('.foo').on('click', function() {
  // do stuff!
});
```
### Module pattern

```javascript
var MODULE = {};

MODULE.submodule = (function() {
  function init() {
    console.log('initiated!');
  };

  return {
    init: init
  };
});
```

### Functional programming

```javascript
// Composition

function add(x) {
  return function(y) {
    return x + y;
  };
};

// Usage
add(1)(2); // 3
```

## How do you organize your code?

This question totally depends on your code base and application. There are a ton
of different ways to organize your code in JavaScript. Some of popular ones
include:

- The Module Pattern
- CommonJS (Browserify)
- Webpack (Modules)
- Observer / Listener
- Factory (maybe you're dealing with angular)

## What's the difference between host objects and native objects?

This is probably better than anything I can write:

[Stack Overflow](http://stackoverflow.com/questions/7614317/what-is-the-difference-between-native-objects-and-host-objects)

## What is the difference between `function Person(){}`, `var person =
Person();` and `var person = new Person()`?

`function Person(){}` is referred to as a *function declaration*. In this case,
it actually serves as a constructor for the Person object or class. When you are
assigning `person = Person()` all you're doing is assigning the value of this
contructor to a variable. When you are calling `new Person()` you are actually
creating a new instance of the Person object by executing the Person constructor
declared initially.

## What's the difference between `.call` and `.apply`?

This one is pretty easy! Both of these functions allow you to set the context of
this for a given function. The only difference is that `.call` takes in
parameters as comma separated values and `.apply` takes in an array.

For example:

```javascript
function foo(){};

foo.call(thisArg, arg1, arg2, arg3, /* ... */);
foo.apply(thisArg, [arg1, arg2, arg2, /* ... */);
```

## Explain `Function.prototype.bind`

`.bind` is a way of setting the `this` value of a function permanently so that
whenever the function is called it has a certain `this` value.

[Mozilla Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)

## When do you optimize your code?

This question can depend on you as a programmer but I think that what they're
trying to get at is that you should never do premature optimizaiton. Addy Osmani
states all the time that you get your piece of functionality to work first, then
you optimize. 

TODO: Find quote for Addy Osmani

## When would you use `document.write()?`

## What's the difference between feature detection, feature inference, and using
the UA string

## Explain AJAX in as much detail as possible

Asynchronous events in the browser
event loop
XHRRequest()
SPA's
pagelets

## Explain how JSONP works and how it's not really AJAX

## Explain 'hoisting'

Hoisting is a property in JavaScript where all functions and variables are
declared in the beginning line of their function scope under the hood. Here's an
example:

```javascript
function foo() {
  console.log(x); // undefined
  var x = 1;
  console.log(x); // 1
};

// What actually happens under the hood

function foo() {
  var x;
  console.log(x); // undefined
  x = 1;
  console.log(x); // 1
};
```

This effect applies to function declarations as well.

## Describe event bubbling

When you add event listener to a parent element and a child element triggers
this event listener the event bubbles up the DOM tree until it hits the node
where that event listener is specified and then it triggers the event handler.

## What's the difference between an attribute and a property?

Pretty simple: *Attributes* are defined by HTML. *Properties* are defined by the
DOM. Some HTML attributes have a 1:1 mapping onto properties (for example id)
But some do not. 

## Why is extending built in JavaScript objects not a good idea?

I think this one is debatable. The answer they are probably looking for is so
that when developers work in your code base the native JavaScript objects behave
exactly as they would expect (no monkey business).

This overall process is referred to as monkey patching and is discussed
(although this is in the ruby world) by DHH in a ruby keynote

[Video Link](http://vimeo.com/17420638)

Be sensible. If you design your objects to spit out fireworks I think you have a
bigger problem at hand. Adding sensible string or array methods won't kill you,
although you could just use your own modules to define them and no one would
complain about that. 

## Difference between document load event and document ready event

`document.load` is fierd when the DOM is loaded but can be prior to images and
other external sources being loaded.

`document.ready` implies that everything has been loaded into the document

## What's the difference between `==` and `===`?

[Standard Stack Overflow](http://stackoverflow.com/questions/523643/difference-between-and-in-javascript)

## Explain the same-origin policy with regards to JavaScript

## Make this work:

```javascript
[1, 2, 3, 4, 5].duplicate();

Array.prototype.duplicate = function() {
  var arr = this.slice(0);

  arr.forEach(function(e) {
    arr.push(e);
  });

  return arr;
}
```

Feel free to come up with a better way!

## Why is it called a ternary expression? What does the word ternary indicate?

A ternary expression uses a ternary operator. A ternary operator means that it
takes three arguments (although in some language implementations this becomes a
conditional expression).

## What is `'use strict';`? What are the advantages and disadvantages for using
it?

[Lol Stack Overflow](http://stackoverflow.com/questions/1335851/what-does-use-strict-do-in-javascript-and-what-is-the-reasoning-behind-it)
