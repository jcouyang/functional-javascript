# Functor
### Functor

`Functor` is a type that can be mapped over. What is the map over?

For instance, the `list` can be considered as a type that can be mapped over. So is mapped over type a enumerable type?

The anwser is no, let's look at the explanation in Haskell. (In fact, all concepts in functional programming can be interpreted under Haskell more clearly.)

```haskell
ghci > :t fmap
fmap :: (a -> b) -> f a -> f b
```

What is the `fmap`, `fmap` is a function that can map over Functor. This function only handle one thing: `(a -> b) -> f a -> f b` (you may interpret this after we have talked about Haskell before), that is given a mapping function from `a` to `b`, and a Functor `f a`, we can get a Functor `f b`.

The definition is easy to read, but is difficult to understand. 

What if I introduce another new word -- "Lift", more confuse eh?

All right, If you combine all of these concepts together, you will understand:

0. Usually, we name the mapping relationship from `a` to `b` as the `map`, using the function as the mapping approach.
0. Similarly, we name function or other type, calculation that can be applied `map` as `Functor`.
0. Such map is a fmap, given a mapping (ie. a function) from set A to set B, there should be a transformation from a computation on A,to a computation on B.
0. If the computation is a function, then this operation is called lifting. Imagine that it lefts the mapping from A to B to their function.

![](http://learnyouahaskell-zh-tw.csie.org/img/lifter.png)

If this image didn't explain all, let me give you a example.

### Example
> **note** Attention, because we haven't implemented `Functor` yet, the code below may not work in your console.

As I said a array is Functor, because the array can be mapped over.
```js
var plus1 = n => n+1;
fmap(plus1, [2, 4, 6, 8])// => [3,5,7,9]
```

Here, array is a Functor type, and the `fmap` transform array [2,4,6,8] by a mapping method 2 -> 3, then it returns [3,5,7.9]. It is easy to understand because the fmap is similar to `array.map`.

Let's try another Functor type, let's say a Function:
```js
var times2 = m => m*2;
fmap(plus1, times2) // => function(){}
fmap(plus1, times2)(3) // => 7 (3*2+1)
```

You may notice the return value of fmap is a function, because the parameter you want to map over is a function `times2`. Remember the equation `(a -> b) -> f a -> f b`, here we can simply substitute what we got here in, `f` is a Function, so we replace `f` by `x->`:

```
(a -> b) -> (x -> a) -> (x -> b)
```

which in this case `x->` is `plus1`, `a->b` is `n->n*2`, so we got:
```
(n->n*2) -> plus1(n) -> plus1(n*2)
```

That means, we map function plus1 over whatever function (n->n*2) return.

Is this a composited function `plus1(times2(3))`? Yes, but function composition is only the tip of an iceberg of Functor, let's look at another Functor.

Functor can be something else, such as Object:
```
fmap(plus1, Either(10, 20))
```

`Either` is also a Functor. Wait for a minute, what type the Either is? All right, before we explain the Either, let's hold on and try to implement Functor in JavaScript first.

### Functor in JavaScript
Firstly, we define a function of Functor type, if 
```js
 var types = function(obj) {
throw new TypeError("fmap called on unregistered type: " + obj);
};
```

Then we implement the function to register Functor:
```js
 Functor = function(type, defs) {
        var oldTypes = types;
        types = (obj) => {
            if (type.prototype.isPrototypeOf(obj)) {
                return defs; 
                // This is the exit of recursion, it checks the type 
                // and determines which Functor the instance of `fmap` registers to.
            }
            return oldTypes(obj); 
            // It searches types recursively, and the efficiency is very low, 
            // because the call stack contains many closures, each closure keeps the type and defs
        }
};
```

And now, we can register a new Functor using `Functor` method, and define its own `fmap` method (Remember we mentioned Functor has only one method). For instance, if we try to turn Array to a Functor, we write:
```js
Functor(Array, {
    fmap: (fn, array) => {
        arr.map(x => fn(x))
    }
})
```

It seems like about to finish. Now we need a function `fmap` for Functor type. This function has two purposes: determining which Functor type the instance belongs to, and invoking its fmap:

```js
fmap = eweda.curry((fn, obj) => {
    return types(obj).fmap(f, obj)
})
```

Similiarly, we can turn a Function to Functor:
```js
Functor(Function, {
    fmap: (f, g) => {
        return eweda.compose(f, g);
}})
```

Do you remember we metioned that fmap is similiar to function composition? Right, here we implement fmap as function composition.

---
To wrap up what `fmap` and Functor really are: `fmap` applies function on Functor, and the Functor can be considered as a container or context with a value. If we want to change the value of x, we use a function mapping `x=>x*2`. If we want to transform an array ,a function, or `Either` that takes context containing value, then we need `fmap` to apply mapping into the value inside them, but still return the same type.

Right now, we may understand the concept of Functor after implementing and using a simple Functor. Then we look back to see what the `Either` is.

[complete code](http://jsbin.com/xezun/1/embed?js,console)

