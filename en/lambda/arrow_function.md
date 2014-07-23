# Arrow Function

Let's look at the features in [ECMAScript 6](http://kangax.github.io/compat-table/es6/), the latest draft of JavaScript that becomes more and more functional. (Although it is still a draft, most of features have been implement in Firefox.)

Why we call it arrow function? I don't know. But if you have seen `->` in Haskell, or `=>` in Scala, you might find all anonymous function used the arrow.

![](http://raider.mountunion.edu/ma/MA125/Spring2011/Chapter7/07s102.jpg)

> **warning** Arrow function is only implemented in Firefox 22+, so that please try our demo code in Firefox. SyntaxError will be thrown even in Chrome.

### Define an Arrow Function

You may define an arrow function in two ways:

```javascript
([param] [, param]) => {
   statements
}

param => expression
```

Simple expression can be writen inline, while multiple statements should be placed in block `{}`.

### Why Using Arrow Function
Let's write a function that doubles each element of array using traditional anonymous function:

```javascript
var a = [1,2,3,4,5];
a.map(function(x){ return x*2 });
```
And we write this function using arrow function:

```javascript
var a = [1,2,3,4,5];
a.map(x => x*2);
```

We only remove `function`, `return` and block, right? If you think the difference is not significate, you may just get used to traditional anonymous function in JavaScript. Howerver, all of `function`, `return` and block do not need to be explicitly existed in our code, since it's just a simple maping of `x` to `x*2`.

Arrow function like ` map(x => x*2)` makes more sense. Because the anonymous function we need has only one thing to do. That is, we need a function `f` that maps `x` to `y`. The best representation for this requirement is `f = (x => x*2)`.

### Lexical `this`

If the reasons we mentioned above can not convince you to change the traditional way you define an anonymous function, please think about how the traditional way will lead to annoying `var self = this` while we're doing OO programming.

```javascript
var Multipler = function(inc){
  this.inc = inc;
}
Multipler.prototype.multiple = function(numbers){
  return numbers.map(function(number){
    return this.inc * number;
  })
}
new Multipler(2).multiple([1,2,3,4])
// => [NaN, NaN, NaN, NaN]
// not worked as we expected,
// because this.inc in map refers to global variable window

Multipler.prototype.multiple = function(numbers){
  var self = this; // refer self to Multipler
  return numbers.map(function(number){
    return self.inc * number;
  })
}
new Multipler(2).multiple([1,2,3,4]) // => [ 2, 4, 6, 8 ]
```
Weird, isn't it? It is a bug in JavaScript, But you may seen lot of such tricky `this` while doing interview tests.

How about using arrow function:

```javascript
Multipler.prototype.multiple = function(numbers){
  return numbers.map(number => number*this.inc)
}

console.log(new Multipler(2).multiple([1,2,3,4])) // => [ 2, 4, 6, 8 ]
```
Woohoo, we don't need  `var self = this` any more.  `this` in arrow function may automatically capture `this` of outer function, in this case, `Multipler`.
