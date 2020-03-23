---
title: Node.js modules (CommonJS modules)
date: 2020-03-22 15:34:33
tags:
---
Javascript has been around for a while and, up until 2015, there were no official ways to deal with code separated in multiple files (modules). Indeed, [ECMAScript 2015](https://en.wikipedia.org/wiki/ECMAScript) (initially known as ES6) contains the first specification from the "ECMAScript Harmony" project which introduce modules, classes, etc.  Sadly, Node.js is not quite ready for the implementation of ECMAScript 2015 modules. In version current version (13.11.0), it's offered as "Stability: 1 - Experimental". Because this state of affairs, multiple libraries and frameworks tried over the years to remediate this issue (CommonJS, ADM, UMD, RequireJS, etc).

NodeJS is currently using __CommonJS__.

Here are some examples:

## main.js
``` javascript
//main.js
const add = require('./add')
const { minus } = require('./minus')
const { mult, div } = require('./other-operators')
const others = require('./other-operators')
const mult2 = require('./other-operators').mult

console.log(add(4,2))
console.log(minus(4,2))
console.log(mult(4,2))
console.log(div(4,2))
console.log(others.mult(4,2))
console.log(others.div(4,2))
console.log(mult2(4,2))
```

Please note the different forms that the variable assignation and __require__ function can take.

<p>&nbsp;</p>

## The action performed by the __require__

When you define the files that will be imported via __require__, you need to keep the following template in mind:

``` javascript
//Template to keep in mind!
var module = { exports: {} };
var exports = module.exports;

// ------------------------------
// WHAT-EVER-IS-IN-YOUR-FILE
// ------------------------------

return module.exports;
```

__Require__ act as if it was creating 2 objects; __exports__ and __module.exports__. The variable __exports__ is just a shortcut to __module.exports__ and they are basically pointing to the same object.  We will see later on that we may find ourselves in a bit of trouble of we miss use this shortcut.

Now that the stage is set, let's actually look at our imported files one by one.

<p>&nbsp;</p>

## The __add.js__ file

``` javascript
//add.js
module.exports = function ( first, second ) {
    return first + second
}
```

This is the easiest form.  The file exports an anonymous function.

In this case, our require in main.js:
``` javascript
//main.js
const add = require('./add')
//...
```

Can be interpreted like this:

``` javascript
//main.js
const add = function ( first, second ) {
    return first + second
}
//...
```
<p>&nbsp;</p>

## The __minus.js__ file

``` javascript
//minus.js
exports.minus = function ( first, second ) {
	return first + second
}
```

Please remember that we are importing it with the following code:
``` javascript
//main.js
//...
const { minus } = require('./minus')
//...
```

Please notice the brackets surrounding our __minus__ function.  In order to understand this one, we will have to explore a new syntax provided by ES6. It's called the [destructuring assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment).

I won't cover this syntax at length here but here's a quick example:

``` javascript
const { p, q } = { a: "test", p: 42, q: true, z: "whatever" };

console.log(p); // 42
console.log(q); // tru
```

As you can see, it's a quick way to extract variables and values from an object.

It's effectively like doing this:

``` javascript
const o = { a: "test", p: 42, q: true, z: "whatever" };
const p = o.p;
const q = o.q;

console.log(p); // 42
console.log(q); // tru
```

So, basically it's extracting variables (destructuring) the object based on its properties.


Then, when we used the following code:

``` javascript
// main.js
//...
const { minus } = require('./minus')
//...
```

Ad the definition in __minus.js__ :

``` javascript
//minus.js
exports.minus = function ( first, second ) {
	return first + second
}
```

Here's how we can interpret that call:

``` javascript
const { minus } = { minus : function ( first, second ) {
	return first + second
}}

// Which is like :
const minus = function ( first, second ) {
	return first + second
}
```

We've created a variable __minus__ pointing to the value of the __minus__ property (which is a function) returned by the require statement.

In summary, this is why we had to use brackets {} to get our function.  The __add.js__ was returning a single function so, we could just pick it up directly into the variable __add__.  But, in the case of the __minus.js__, the __require__ was returning an object and we had to destructure the object into our __minus__ variable.

<p>&nbsp;</p>

## The _other_operators.js_ file

``` javascript
//other_operators.js
function multiplication (first, second) {
	return first * second
}

function div (first, second) {
	return first / second
}

module.exports = {
	mult: multiplication,
	div: div
}
```

We can apply pretty much the same logic than we used for destructuring the object returned by __require__ in the __minus.js__ file, except that we have 2 functions:

``` javascript
//main.js
//...
const { mult, div } = require('./other-operators')
//...
```

For the next line in __main.js__, we wanted to show that we do need to destructure the object returned by __require__:

``` javascript
//main.js
//...
const others = require('./other-operators')
//...
```
We just have to remember that the variable __others__ is an object, not a function.  So we have to call the mult and div function this way:

``` javascript
//main.js
//...
console.log(others.mult(4,2))
console.log(others.div(4,2))
//...
```

The next one is a bit ugly but I have seen this in the past...

``` javascript
//main.js
//...
const mult2 = require('./other-operators').mult
//...
```

It's basically like getting the value of __mult__ (function) directly from the object returned by __require__...

``` javascript
const johnsname = { name: "john" }.name
console.log(johnsname) //resulting in "john"
```
<p>&nbsp;</p>

## Things to avoid

As we pointed out earlier, the __exports__ variable is just a shortcut for __module.exports__.  They are both pointing at the same object.  It's easy to get wrapped up in your code and do something like this:

``` javascript
exports = { test : function () { console.log ("ops!"); } }
```
The problem with this, is that you just moved the pointer of exports to a new object so __module.exports__ and __exports__ are no longer pointing at the same object... but the __require__ statement will only return the object that __module.exports__ is pointing to!


<p>&nbsp;</p>

## Lost in space?

If you become unsure of what is on your __module.exports__, you can always _temporarily_ add the following code at the end of the file and run it through node.

``` javascript
console.log(process.mainModule.exports)
```

For example, if we add this line at the end of __other-operators.js__, we would get the following output:
``` javascript
{ mult: [Function: multiplication], div: [Function: div] }
```

## Summary

In summary, just keep in mind the following pattern:

``` javascript
//Template to keep in mind!
var module = { exports: {} };
var exports = module.exports;

// ------------------------------
// WHAT-EVER-IS-IN-YOUR-FILE
// ------------------------------

return module.exports;
```

Also, make sure you understand the  [destructuring assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment).

If you are in doubt, temporary add this code to your file and run it through node:

``` javascript
console.log(process.mainModule.exports)
```

Finally, if you are returning an object and using the __exports__ variable shortcut instead of the __module.exports__ variable, make sure that you are adding properties to the object, not overriding it.  And if you are just returning a function, use the __module.exports__.  And, at last, if you just don't want to bother, just use __module.exports__!