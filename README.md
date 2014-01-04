# JavaScript Styleguide

It's important to write code that is consistent and thus maintainable. Follow these rules.

(formatting and some rules based on https://github.com/aheckmann/js-styleguide) 

## Table of Contents

* [Tabs vs Spaces](#tabs-or-spaces)
* [Line Length](#line-length)
* [Whitespace](#whitespace)
* [Braces](#braces)
* [Parenthesis](#parenthesis)
* [Variable and Property Names](#variable-and-property-names)
* [Constructor Names](#constructor-names)
* [Function Spacing](#function-spacing)
* [Semi-colons](#semi-colons)
* [Variables](#variables)
* [Self](#references-to-this)
* [Errors](#errors)
* [Return Early](#return-early)
* [Inline Documentation](#inline-documentation)
* [Modifying Native Prototypes](#modifying-native-prototypes)
* [CoffeeScript](#coffeescript)
* [Use Strict](#use-strict)
* [JSHint](#jshint)
* [Single/Double Quotes](#single-quote-vs-double-quote)

## Coding Guide

### Tabs or Spaces

Spaces, always. 4 spaces because our monitors are huge.

### Line Length

**80** characters max.

### Whitespace

No trailing whitespace allowed. Clean up after yourself.

### Braces

Place opening braces on the same line as the statement.

```js
if (areWeWritingJava)
{
  // hmmmm
}

if (nicelyDone) {
  // ok!
}
```

### Parenthesis

Leave space before and after the parenthesis for `if/while/for/catch/switch/do`

```js
// inconsistent
if(weAreHavingFun){
  console.log('Wheeee!');
}

// consistent
if (weAreHavingFun) {
  console.log('Wheeee!');
}
```

### Variable and Property Names

Use [lower camel case](http://en.wikipedia.org/wiki/camelCase#Variations_and_synonyms) and be descriptive. Aim for one word descriptions if possible.

```js
// inconsistent
var plan_query = model.findById(plan_id);

// consistent
var planQuery = model.findById(planId);
```

### Constructor Names

Constructors should use [upper camel case](http://en.wikipedia.org/wiki/camelCase#Variations_and_synonyms) to hint to its intended use.

```js
function Movie(id, title) {
  if (!(this instanceof Movie)) {
    return new Movie(id, title);
  }

  this.id = id;
  this.title = title;
};

var gravity = new Movie(1, 'Gravity');
```

### Function Spacing

When executing a function, leave no space between it's name and parens.

```js
eatIceCream (); // inconsistent

eatIceCream();  // consistent
```

Leave a space between your `function` declaration/expression parenthesis and the opening `{`.

```js
function eatIceCream(){
  // inconsistent
};

function eatIceCream() {
  // consistent
};
```

There should be no empty lines between the function name and the first line of code.

```js
// inconsistant
function eatIceCream() {

  var dessert = bowl().of(iceCream);
  human.eat(desert);
}

// consistant
function eatIceCream() {
  var dessert = bowl().of(iceCream);
  human.eat(desert);
}
```

### Semi-colons

Use em.

```js
var semis = function() {
  // codez
}

['property'].forEach(console.log) // TypeError: Cannot call method 'forEach' of undefined
```

```js
var semis = function() {
  // codez
};   // <--------

['property'].forEach(console.log); // property 0 [ 'property' ]
```

It's really great if you take the extra time to
[learn where they're _actually_ necessary](http://blog.izs.me/post/2353458699/an-open-letter-to-javascript-leaders-regarding),
you may just be surprised how few places there are. I personally think it's easier to
learn the rules and use them only where they're required, but for even mid-size teams, it's
simpler to just use 'em and move on.

### Variables

When declaring multiple variables, use "var first" style.

```js
var fix = true;
var nice = 'you best';
var woot = require('woot');
```

This makes for easier copy/paste later on and less futzing around with semi-colons and commas.

## References to `this`

Name variable references to `this` "self".

```js
Dancer.prototyp.selfie = function selfie() {
  var self = this;
  return function() {
    console.log(self);
  };
};
```

## Errors

If you must throw an error, make sure what you throw is an instance of `Error`.

For example:

```js
if (!exists) {
  throw 'invalid!'; // not very helpful
}

if (!exists) {
  throw new Error('invalid!'); // more helpful
}
```

Each instance of `Error` has a `stack` property which aids in debugging.

```js
try {
  throw new Error('hmmmm');
} catch (err) {
  console.error(err.stack);
}
```

Can I throw:

- `strings`? nope
- `numbers`? no
- `arrays`? nadda
- `objects` that have a message property? forget it
- `up`? please not on me

[More context](http://www.devthought.com/2011/12/22/a-string-is-not-an-error/)

Same goes for callbacks. If an error condition is identified, pass an instance of `Error`
to the callback.

```js
app.use(function(req, res, next) {
  var err = failedValidation(req)
    ? new Error('could not parse request')
    : undefined;

  next(err);
});
```

### Return Early

Return early whereever you can to reduce nesting and complexity.

```js
function checkSomething(req, res, next) {
  if (req.params.id) {
    if (isValidId(req.params.id)) {
      find(req.params.id, function(err, doc) {
        if (err) return next(err);
        res.render('plan', doc);
      });
    } else {
      next(new Error('bad id'));
    }
  } else {
    next();
  }
};

// return early
function checkSomething(req, res, next) {
  if (!req.params.id) {
    return next();
  }

  if (!isValidId(req.params.id)) {
    return next(new Error('bad id'));
  }

  find(req.params.id, function(err, doc) {
    if (err) return next(err);
    res.render('plan', doc);
  });
};
```

## Inline Documentation

Each function should have corresponding JSDoc-style comments that include
parameters and return values. Examples are always appreciated.

```js
/**
 * Adds two numbers.
 *
 * Example
 *
 *     var result = add(39, 12);
 *
 * @param {Number} num1
 * @param {Number} num2
 * @return {Number}
 */

function add(num1, num2) {
  return num1 + num2;
};
```

### Modifying Native Prototypes

Don't do it. It causes debugging headaches, breaks expected behavior and introduces potential incompatibility with new versions of V8.
There's always a better way.

```js
// please no
Array.prototype.contains = function(needle) {
  return this.indexOf(needle) > -1;
};

// For example, create a helper module with a function that accepts an array:

// helpers/array.js
exports.contains = function(array, needle) {
  return array.indexOf(needle) > -1;
};
```

## CoffeeScript

Is banned. Just learn JavaScript. Really. Its a simple language.

## Use Strict

Always. No exceptions. At the top of every file
```js
'use strict';
```

## JSHint

All files must pass jshint validation, and it's built into our gruntfile to aid with this. 

If you love yourself, you'll install something in your editor to help with this inline.

Rules:

```json
{
    "node": true,
    "browser": true,
    "jquery": true,
    "eqeqeq": true,
    "indent": 4,
    "latedef": true,
    "quotmark": "single",
    "trailing": true,
    "undef": true,
    "unused": true,
    "curly": true,
    "camelcase": true,
    "strict": true
}
```

## Single Quote vs Double Quote

We always use single quotes in JS files because that's what I decided. There doesn't seem to be a winner

(http://stackoverflow.com/questions/242813/when-to-use-double-or-single-quotes-in-javascript)

However, JSON files require double-quotes to be validated, so pure JSON files use double.



