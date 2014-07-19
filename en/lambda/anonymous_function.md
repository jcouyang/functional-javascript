# Anonymous Function

Supporting anonymous function means we can place a function as first order member. So that a function can be passed as a parameter, and returned as a value. JavaScript is a functional programming language. It supports first order function and allow us to define an anonymous function easily.

### Define Anonymous Function

```js
function(x){
    return x*x;
}// => SyntaxError: function statement requires a name
```

Why we get a SyntaxError here? Because we need a function expression to create an anonymous function.

A expression returns value:

``` js
var a = new Array()  // new Array() is an expression, and the whole line is called a statement.
```

But why the error says: `function statement requires a name`. In JavaScript, there is another way to declare a function, that is using function statement. A statement requires a name. The code above is treated as function statement, because it returns nothing. Therefore, we got the error.

If we assign `function (x){return x*x}` to a variable, or pass it as a parameter, we will not get any SyntaxError, because `function (x){return x*x}` is only a expression under this circumstance. For instance:

```js
var squareA = function(x){
    return x*x;
}
```

However, a tricky thing is that `squareA` becomes a named function.

```
console.log(squareA)  // => function squareA()
```

Even `squareA` becomes a named function, but the definition process is different from code below:

```js
function squareB(x){
    return x*x;
} // => undefined
```

`squareB`  directly defines a function using function statement that obviously has no return. While, `squreA` defines an anonymous function using function expression, and assigns the function returned from function expression to a variable named `squareA`. So, a expression returns a value:

```
console.log(function(x){ return x*x});
// => undefined
// => function ()
```

The `undefined` is the return value for `console.log()`, and `function()` is the return value from function expression that defines an anonymous function.

### Use Anonymous Function

Function is treated as first order, that means we can assign a function to a variable:

```js
var square = function(x) {return x*x}
```

We also pass the anonymous function as a parameter, like before we passed an anonymous function to ` console.log`:

```js
 console.log(function(x){return x*x})
```

And, we return a function as a return value:

```js
function multiply(x){
    return function(y){
        return x*y;
    }
}

multiply(1)(2) // => 2
```