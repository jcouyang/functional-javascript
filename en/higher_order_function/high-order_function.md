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

Have you feel the flexible of higher-order function and anonymous function combination?


###Function as return value
The return value of a function can not just be a value, but also can be a function. Let's look at the `aliasFor`, an utility function in [Ramda](), which is used to give an alias for certain member in function E.

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
There are two `return`s, the one is that `fn` returns itself, the another is that `aliasFor` also returns `fn` and gives `fn` a few of alias like `fn.is`, `fn.are` ...

What is the meaning of two returns? Returning `fn` from `fn` simply means `fn() => fn`, so that `fn()() => fn() => fn`, etc. No matter how many time the `fn` is invoked, the final return value is still `fn`.

![Imgur](http://i.imgur.com/1qUCC8s.png)

How do we use this? We make `E[newName] = E[oldName]` by using the side affect of `fn`, giving a alias for the specific member of E. So that each invocation of `fn` will give an alias name to `E`. The second `return` assigns some alias names for `fn` itself, so we can wirte more readable chain-code:

```js
aliasFor('reduce').is('reduceLeft).is(foldl)
```

> **Note** We will explain more on importance of function as a return value, Currying, and closure in later chapters. Let's look at typical application of higher-order function as passing value.

[method]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function "function"
