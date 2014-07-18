# Arrow Function

Let's look at the features in [ECMAScript 6](http://kangax.github.io/compat-table/es6/), the latest draft of JavaScript that becomes more and more functional. (Although it is still a draft, most of features have been implement in Firefox.)

Why we call arrow function at first line? I don't know. But if you once wrote `->` in Haskell, or wrote `=>` in Scala, you might find all anonymous function used the arrow.

**Arrow function is only implemented in Firefox 22+, so that please try our demo code in Firefox. SyntaxError will be thrown even in Chrome.**

### Define an Arrow Function

You may define an arrow function in two ways:

```javascript
([param] [, param]) => {
   statements
}

param => expression
```

Simple expression can be write as inline, while multiple statements should be placed in block `{}`.

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

We only remove `function`, `return` and block, right? If you think the difference is not significance, you may just get used to traditional anonymous function in JavaScript. `function`, `return` and block do not explicitly exist in our code.
 只是少了` function` 和` return` 以及 block, 不是吗? 如果觉得差不多, 因为你看惯了 JavaScript 的匿名函数, 你的大脑编译器自动的忽略了,因为他们不需要显示的存在.
 
 However, ` map(x => x*2)` makes more sense. Because the anonymous function we need is supposed to do only one thing. That is, we need a function `f` that maps `x` to `y`. A best representation for this requirement is `f = (x => x*2)`. 

### Lexical `this`

If the reasons we mentioned above can not convince you to change the traditional way you define an anonymous function, please think about how the traditional way makes us annoyed by using `var self = this` .

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
// not worked as we expected, because this.inc in map refers to global variable window

Multipler.prototype.multiple = function(numbers){
  var self = this; // refer self to Multipler
  return numbers.map(function(number){
    return self.inc * number;
  })
}
new Multipler(2).multiple([1,2,3,4]) // => [ 2, 4, 6, 8 ]
```
Weird, right? 确实是 Javascript 的一个 bug. This become a question in many interviews.

How about using arrow function:

```javascript
Multipler.prototype.multiple = function(numbers){
  return numbers.map(number => number*this.inc)
}

console.log(new Multipler(2).multiple([1,2,3,4])) // => [ 2, 4, 6, 8 ]
```
Joyful, right? because we don't need  `var self = this` any more. This time, `this` in arrow function may automatically capture `this` of outer function, aka. `Multipler`.