---
title: javascript-this
tags: [ Javascript]
categories: [General Coding]
---

There are few subjects in javascript more confusing than the keyword ```this```.  There has been countless blogs, video, tirade, etc. regarding _this_ subject.  I'm tossing my hat in the ring with this post. Here we go.

First off, the difinition:

__In javascript, the ```this``` keyword is a general reference holder that applies to different values based on scope and how it is called.__

## The ```this``` keyword in Node.js

In Node.js all modules (script files) are executed in their own closure.

In other words, in just about any file running in Node, this will just be an empty object, as Node wraps the code in an anonymous function that is called immediately, and you'd access the global scope within that context with GLOBAL instead.

However, when calling a function without a specific context in Node.js, it will normally be defaulted to the global object - The same GLOBAL mentioned earlier, as it's execution context.

So outside the functions, this is an empty object, as the code is wrapped in a function by Node, to create it's own execution context for every module (script file), while inside the functions, because they are called with no specified execution context, this is the Node GLOBAL object

```javascript
var obj1;
var obj2;

function x() {
    obj1 = this; // GLOBAL
}

function y() {
    obj2 = this; // GLOBAL
}

x();
y();

console.log(obj1 === obj2);  // GLOBAL === GLOBAL = true
console.log(obj1 === this);  // GLOBAL === {} = false
```

## What about ```"use strict";```?

The strict mode in javascript changes the behavior when it comes to the default assignment for the ```this``` keyword.

You can read a quite detailed explanation from the [Mozilla Developer Network (MDN)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode) under the header __"Securing" JavaScript__ but I'll try to paraphrase here:

When you are not using ```"use strict";``` (what some may refer to as being in "__sloppy mode__"), the ```this``` keyword is always forced into being an object. By default, this object is the global object.

When ```"use strict";``` is applied, javascript is free to just use ```undefined``` for the this object.

That means, among other things, that in browsers it's no longer possible to reference the window object through ```this``` inside a strict mode function.

## There a two rules

- The keyword ```this``` from a function statement/declaration always refers to the global object (__window__ in a browser, __global__ in Node.js).  Please note that this rule just applies to a __function statement/declaration__, not to arrow functions (__=>__) or function expressions (i.e. ```const fnct = function () {} ```)

- The keyword ```this``` is declared as a method of an object, the ```this``` keyword refers to the object. This rule is often explained by saying that ```this``` refers to the object on the left of the dot when you call it.

## Explanation and some examples



## What about arrow function (=>)

## Sources

Youtube:
[Zac Gordon](https://www.youtube.com/watch?v=2qMKjWf1KdE)
[Techsith](https://www.youtube.com/watch?v=NV9sHLX-jZU&t=782s)
[Programming with Mosh](https://www.youtube.com/watch?v=gvicrj31JOM)

Website:
[Strict mode](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode)

Stack Overflow:
[The 'this' keyword behaves differently in Nodejs and browser](https://stackoverflow.com/questions/34838659/the-this-keyword-behaves-differently-in-nodejs-and-browser)


