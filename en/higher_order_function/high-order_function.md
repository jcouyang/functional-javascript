# Higher-order function
Using the higher-order function is very convenient in JavaScript.

### Function as parameter
Supposing we want to sort an array, using the familiar `sort`.

```js
[1,3,2,5,4].sort( (x, y) => x - y )
```
If we want to sort the array in descending order, we just pass `(x, y) => y - x` simply. If there is an array containing Objects, and we want to sort by `Object.id`, we may write as following:

```js
[{id:1, name:'one'},
 {id:3, name:'three'},
 {id:2, name:'two'},
 {id:5, name:'five'},
 {id:4, name:'four'}].sort((x,y) => x.id - y.id)
 ```

Have you feel the flexibility when composing higher-order function and anonymous function?


###Return function as value
The return value of a function can not just be a value, but also can be a function. Let's look at the `aliasFor`, an utility function in Eweda, which is used to give an alias for certain member in function E.

> **Note** Does this sound wired? How can a function contain a member? The function in JavaScript, in fact, is a special Object. Try typing `console.log.` in Firefox console, have you noticed some members in Intellisense, however, `typeof console.log` indicates `console.log` is just a function.

```js
var E = () => {}
var aliasFor = oldName => {
    var fn = newName => {
      E[newName] = E[oldName];
      return fn;
    };
    return (fn.is = fn.are = fn.and = fn);
};
```
There are two `return`s, the first one is that `fn` returns itself, the another is that `aliasFor` also returns `fn` and gives `fn` a few of alias like `fn.is`, `fn.are` ...

What is the meaning of returning `fn` it self? Returning `fn` from `fn` simply means `fn() => fn`, which means `fn()() => fn() => fn`, etc. No matter how many time the `fn` is invoked, the final return value is still `fn`.

![Imgur](http://i.imgur.com/1qUCC8s.png)

Why we doing this? We can make `E[newName] = E[oldName]` by using the side affect of invoking `fn`. So that each invocation of `fn` will give an alias name to `E`. The second `return` assigns some alias names for `fn` itself, so it became more readable by chaining the function:

```js
aliasFor('reduce').is('reduceLeft).and(foldl)
```

> **Note** We will explain more about how importance of returning function as a value, Currying, and closure in later chapters. Let's look at typical application of passing function to a higher-order function.
[method]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function "function"
