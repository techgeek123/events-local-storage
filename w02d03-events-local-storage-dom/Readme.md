# Events, Local Storage and DOM

## Learning Competencies
- Learn about browser events
- Compare and contrast event capturing and bubbling
- Describe what the event object is and name a few commonly used properties that it includes
- Learn about localStorage and sessionStorage
- Add and remove to/from localStorage - primitives & objects
- DOM and manipulation of it with document object
- Iterate over DOM elements and add attributes

## Overview

### Events

Throughout this chapter we'll be using the following HTML as our working example. Feel free to copy and paste this text into your text editor, then open the page in Google Chrome.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <button class="first_button" onclick="firstClick()">Click me first!</button>
    <button class="second_button" >Click me second!</button>
    <button class="third_button">Click me third!</button>
</body>
</html>
```



#### What's an event?
From [MDN](https://developer.mozilla.org/en-US/docs/Web/Events)

> DOM Events are sent to notify code of interesting things that have taken place. Each event is represented by an object which is based on the Event interface, and may have additional custom fields and/or functions used to get additional information about what happened. Events can represent everything from basic user interactions to automated notifications of things happening in the rendering model.

In a nutshell, events are things we can trigger by interacting with the browser. Here are some potential events we can listen (and then execute some code) for:

- clicking on something
- hovering over something with the mouse
- pressing certain keys
- when the DOM has loaded
- when a form is submitted

You can see a full list [here](https://developer.mozilla.org/en-US/docs/Web/Events).
#### Code Along
##### Adding an event listener
There are three ways that we can add event listeners to our code. Given an element we want to listen to, and a function we want to execute, we can either attach the name of the function to the element in HTML, in JavaScript, or we can use the `addEventListener` method.

Here's some JavaScript code that highlights each one of these approaches:
```js
// Option 1: - directly in HTML. Note that in your HTML, 
// the first button makes reference to a function called firstClick 
// in the onclick attribute
function firstClick(){
    alert("you clicked the first button!");
}

// Option 2 - attach the function to the element
var secondButton = document.querySelector('.second_button');
secondButton.onclick = function(){
    alert("you clicked the second button!");
}
// Option 3 - use addEventListener
var thirdButton = document.querySelector('.third_button');
thirdButton.addEventListener("click", function(){
    alert("you clicked the third button!");
});
```
So which one is best?

It is often best to either use the second or third option, as these are a bit more customizable, but it is good to know all of your options. In this course, we will primarily be using `addEventListener`.

Let's go back to our initial example, remove the onclick attribute from our first button, and add some event listeners to our buttons.
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <button class="first_button">Click me first!</button>
    <button class="second_button" >Click me second!</button>
    <button class="third_button">Click me third!</button>
</body>
</html>
```
Just like when changing multiple elements, we need to iterate through each one - we cannot just add an event listener to an array-like object of element nodes.
```js
var buttons = document.getElementsByTagName("button");

// this will NOT work - what kind of error do you get?
buttons.addEventListener("click", function(){
    alert("You just clicked a button");
});

// we have to add the event listener to each button
for(var i=0; i<buttons.length; i++){
    buttons[i].addEventListener("click", function(){
        alert('You just clicked on a button!');
    });
}
```
##### Removing an event listener
Sometimes we want to stop listening for events. To do that we use the `removeEventListener` method, but it has some quirks you should be aware of. Assuming you've still got those alert messages being triggered when you click on a button, let's take a look at the following code:
```js
var buttons = document.getElementsByTagName("button");

// this will NOT work 
buttons.removeEventListener("click", function(){
    alert("You just clicked a button");
});

// we have to remove the event listener to each button but this STILL won't work! That is because we are using an annonymous function
for(var i=0; i<buttons.length; i++){
    buttons[i].removeEventListener("click", function(){
        alert('You just clicked on a button!');
    });
}
```
In order to successfully remove an event listener, the callback that we pass in can't be an anonymous function. Let's clear out all the JavaScript and start from scratch. The code below should work; if you run it, event listeners will be added and then removed, so that nothing happens when you click on a button.
```js
// In order to remove, we have to name our function before adding it instead of adding an anonymous function
function alertData(){
    alert("You just clicked a button");
}

for(var i=0; i<buttons.length; i++){
    buttons[i].addEventListener("click", alertData);
}

// since we have named our function, we know which one to remove
for(var i=0; i<buttons.length; i++){
    buttons[i].removeEventListener("click", alertData);
}
```
##### Adding an event to wait for the DOM to load
Another issue that we may face is when we have code like this:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <script>
        var container = document.getElementById("container");
        container.innerText = "Hello World"; // This throws an error!
    </script>
</head>
<body>
    <div id="container">

    </div>
</body>
</html>
```
What's going on here?? Well, since our `script` tag is running before the DOM has loaded, it has no idea what the `<div id="container"></div>` is! What we can do is wait for the DOM to load before executing any JavaScript. This can be done either with the load event or the `DOMContentLoaded` event. You can read about the difference between them here.
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <script>
        document.addEventListener("DOMContentLoaded", function(){
            var container = document.getElementById("container");
            container.innerText = "Hello World"; // This works now!
        });
    </script>
</head>
<body>
    <div id="container">

    </div>
</body>
</html>
```



### Local Storage

Local storage is more secure, and large amounts of data can be stored locally, without affecting website performance. Unlike cookies, the storage limit is far larger (at least 5MB) and information is never transferred to the server.

`localStorage` is a mechanism for storing information in the browser for a specific domain. The API is quite easy to use and very minimal - so let's get started with it!

You can read more about it [here](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API).

#### localStorage vs sessionStorage
When you read more about `localStorage` you will also hear about something called `sessionStorage`. [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API) explains them both this way:

> The localStorage property allows you to access a local Storage object. localStorage is similar to sessionStorage. The only difference is that, while data stored in localStorage has no expiration time, data stored in sessionStorage gets cleared when the browsing session ends—that is, when the browser is closed.

What this basically means is:

- `sessionStorage` maintains a separate storage area for each given origin that's available for the duration of the page session (as long as the browser is open, including page reloads and restores)

- `localStorage` does the same thing, but persists even when the browser is closed and reopened.
# Code Along
## Adding, retrieving and removing from localStorage
The most important thing to remember is that all of your keys in `localStorage` or `sessionStorage` must be **STRINGS**. `localStorage` stores everything as strings, so it's just good to get in the habit of setting your keys as strings to avoid confusion.

To set something into localStorage we use the `setItem` method and to retrieve we use the `getItem` method (only passing in the key)
```js
localStorage.setItem("instructor", "Elie");
localStorage.setItem("favoriteNumber", 18);
localStorage.setItem("isHilarious", true);

localStorage.getItem("instructor"); // "Elie"
```
If you refresh the page - you should be able to still retrieve these keys in `localStorage`!

To delete a key we use the `removeItem` function, and to clear everything from `localStorage` for this domain we use clear:
```js
localStorage.removeItem("instructor");
localStorage.clear();
```
#### Adding objects
So far we have only added primitives, which is nice and easy, but what happens when we start adding objects? Well, things get a bit trickier.... Let's see what happens:
```js
var instructors = ["Elie", "Matt", "Tim"];

localStorage.setItem("instructors", instructors);
localStorage.getItem("instructors");
```
When we get the instructors from localStorage, we don't have an array anymore - we have a string! Remember, when dealing with localStorage, everything gets converted into a string.

In order to get back our original data type, we need to briefly introduce something called JSON. Right now we are going to cover this quickly, but we will be discussing it quite a bit more later on.

From [JSON.org](http://www.json.org/):

> JSON (JavaScript Object Notation) is a lightweight data-interchange format. It is easy for humans to read and write. It is easy for machines to parse and generate.

JavaScript has a built-in JSON object, and on this object are two methods used to convert JavaScript data into JSON, and to parse JSON strings into JavaScript. The JSON object itself can't be called or constructed, and aside from its two methods it has no interesting functionality of its own. You can read more about it [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON)

- `JSON.stringify` https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify is used to convert JavaScript to JSON (or stringify)
- `JSON.parse` parses a string as JSON. https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse

Using these two methods allows us to preserve the intended data structure when it is something other than a string:
```js
var instructors = ["Elie", "Matt", "Tim"];

// the JSON.stringify call converts the instructors array into a JSON string
localStorage.setItem("instructors", JSON.stringify(instructors));

// JSON.parse converts the JSON string back into JavaScript (in this case, a valid array)
JSON.parse(localStorage.getItem("instructors"));
```


## DOM

#### Wiki Says
The Document Object Model (DOM) is a cross-platform and language-independent application programming interface that treats an HTML, XHTML, or XML document as a tree structure wherein each node is an object representing a part of the document. The objects can be manipulated programmatically and any visible changes occurring as a result may then be reflected in the display of the document.

#### What is the DOM?
From *[MDN](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model)*:

> The Document Object Model (DOM) is a programming interface for HTML, XML and SVG documents. It provides a structured representation of the document as a tree. The DOM defines methods that allow access to the tree, so that they can change the document structure, style and content. The DOM provides a representation of the document as a structured group of nodes and objects, possessing various properties and methods. Nodes can also have event handlers attached to them, and once an event is triggered, the event handlers get executed. Essentially, it connects web pages to scripts or programming languages.

To access the DOM, we make use of the `document` object. This object has properties and functions that we use to access our HTML elements which we can manipulate with JavaScript.

Working with the DOM is one of the more fun parts about using JavaScript because we can add behavior to our web pages. With JavaScript, we can change how a page looks and functions based on events the user makes (clicking, hovering, submitting a form, typing a certain key). As you walk through the next couple sections, think about what you might be able to build with this new knowledge.

#### How to access elements in the DOM
Let's get started with this sample HTML. Copy and paste this into a file called `index.html` and add an external script tag, or use a `<script></script>` right before the `body` closes. For simplicity, we'll take the latter approach right now.
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <!-- make sure for now that your script tag is in the head -->
    <title>Document</title>
</head>
<body id= "container">
    <div class="hello">
        Hello World
    </div>
    <div class="hello">
        Hello Everyone!
    </div>
    <a href="#">This link goes nowhere!</a>
    <button>Click me!</button>
    <script>
        // you can put your JavaScript in here, or include an external file.
    </script>
</body>
</html>
```
The easiest way to select elements is by their `id` using the `getElementById` function on the `document` object (`document.getElementById`). This returns a **SINGLE** element (because ids must be unique!).
```js
var container = document.getElementById("container");
```
We can also use a function called `querySelector`, which selects a **SINGLE** element using CSS selectors. If multiple elements match the query you pass in to `querySelector`, the function will simply return the first matching element that it finds.
```js
var container = document.querySelector("#container");
```
Notice that when we select by id using `querySelector`, we pass in the string `#container`, not `container`. Remember: `querySelector` always expects a CSS selector. In contrast, when we use `getElementById`, we just pass in the string container (no hashtag)! Since `getElementById` expects to find an element by id, in this case the hashtag isn't necessary.

To select multiple elements, we can use `getElementsByTagName` or `getElementsByClassName`, or we can use `querySelectorAll` and pass in a CSS selector. These will return what appear to be arrays (they are not exactly arrays, but for right now, that is not a problem).
```js
var divs = document.getElementsByTagName("div");
var divs = document.querySelectorAll("div");
```
Here is another example using `getElementsByClassName` and the same thing with `querySelectorAll`.
```js
var divsWithClassOfHello = document.getElementsByClassName("hello");
var divsWithClassOfHello = document.querySelectorAll(".hello");
```
As you can see, when you pass in a class name using `getElementByClassName`, you don't need to start the string with a dot. The function expects to receive a class name - it's in the name of the function! On the other hand, `querySelectorAll` takes in any valid CSS query, which is why you need to pass in .hello if you want it to find all elements with a class of `"hello."`



## Exploration
- Check out a *code along* tutorial by tutorials point [here](https://www.tutorialspoint.com/javascript/javascript_events.htm).
- A blog on **JavaScript Events** by *quirksmode.org*. Read [here](https://www.quirksmode.org/js/introevents.html)
- Read a blog **How to use local storage by Javascripts** by *Sara Vieira*. Read [here](https://www.webdesignerdepot.com/2013/04/how-to-use-local-storage-for-javascript/).
- Check out a blog on **DOM Manipulations** [here](http://callmenick.com/post/basics-javascript-dom-manipulation)
- An article **15 JavaScript Methods For DOM Manipulation for Web Developers** by *Preethi Ranjit*. Read [here](http://www.hongkiat.com/blog/dom-manipulation-javascript-methods/)
