# map fold filter

We will talk about the typical usage of higher-order function: `map`, `fold/reduce` (in [my](https://github.com/jcouyang) opinion, the name fold is more suitable than the name reduce, although they may be slightly different in Scala or Haskell).

![](http://www.backpacker.com/2007/images/september2010/mapfolding_200x170.png)

`map` applies a specific function on each element of list, then returns a new list.

`fold` folds all of the elements, and finally returns a single value, a single array or a single Object. We have two types of `fold`: `foldl` folds elements from left to right, while, `foldr` folds elements from right to left.

`filter` is as straightfoward as it's name. it filters elements according to certain condition.

### fold left
Let's implement `foldl` first, and you will find the reason why I implement `foldl` first.

The following code is come from [Eweda](https://rawgit.com/CrossEye/eweda/master/docs/eweda.html):
```js
var foldl = function(fn, acc, list) {
    return (isEmpty(list)) ? acc : foldl(fn, fn(acc, head(list)), tail(list));
};
aliasFor("foldl").is("reduce");
```

Function `foldl` accepts three parameters:

* a function `fn` which accepting two parameters: `acc` and a list,
* an accumulated value `acc`,
* finally the `list` we need to apply `fold` on.

This implementation is a simple recursion, because `foldl` invokes itself.

0. The terminal condition of recursion is when `isEmpty(list) === true`, then it returns `acc`,
0. otherwise, it applies `acc` and the first element of list `head(list)` to `fn`, and passes the result as new `acc` into `foldl`,
0. then, it passes the remaining list `tail(list)` as new list into `foldl`


And you may probably notice the fact that it's actually a **tail recursion**.

> **Note**Why we use tail recursion?

0. We will get the result of each calculation for `fn(acc, head(list)`, and pass it to next invocation as parameter
0. so we can get the final result directly at the end of recursion, without waiting for the stack to pop out.

Right now, we have a working `foldl` function, let's look at how easy to implement the `foldr`.
```js
var foldr = function(fn, acc, list) {
    return (isEmpty(list)) ? acc : fn(head(list), foldr(fn, acc, tail(list)));
}
```

`foldr` is not a tail recursion, let's find out what kind of differences between non-tail recursion and tail recursion.

Supposing we want to fold `[1,2,3]` with addition, we fold the list as following steps:
```js
foldl((a, b) => a+b, 0, [1,2,3])
```
| acc |head| tail |
| -- | --| -- |
| (0,1) => 0+1 = 1 | 1| [2,3] |
| (1,2) => 1+2 = 3 | 2| [3] |
| (3,3) => 3+3 = 6 | 3| [] |

Observe the steps of `foldr`:
```js
foldr((a, b) => a+b, 0, [1,2,3])
```
> **note** To brief the description, we replace `(a, b) => a+b` with `fn`

| acc |head| tail |
| -- | --| -- |
| (1,foldr(fn, 0, [2,3])) => 1 + foldr(fn, 0, [2,3])| 1| [2,3] |
| (1,(2, foldr(fn, 0, [3]))) => 1 + (2 + foldr(fn, 0, [3]))| 2| [3] |
| (1,(2, (3, foldr(fn, 0, [])))) => 1 + (2 + (3 + foldr(fn, 0, [])))| 3| [] |
| (1,(2, (3, foldr(fn, 0, [])))) => 1 + (2 + (3 + 0))| 3| [] |
| (1,(2, (3, foldr(fn, 0, [])))) => 1 + (2 + (3))| 3| [] |
| (1,(2, (3, foldr(fn, 0, [])))) => 1 + (5)| 3| [] |
| (1,(2, (3, foldr(fn, 0, [])))) => 6| 3| [] |

Now you may see the different between non-tail recursion and tail recursion clearly. Tail recursion returns final result as soon as it finished the final invoke. Non-tail recursion got the result from `foldr(fn, 0, [])`, then, do the calculation during the way back. (Have you noticed that each group of parentheses is one level of recusion, aka. the depth of stack)

### map
The implementation of `map` as follow:
```js
var map = function(fn, list) {
    return reverse(foldl((acc, x) => prepend(fn(x), acc), [], list));
};
```

Why we use `foldl` here? Let's explain it:
```js
map(a => a+2, [1,2,3])
```
| acc |head|tail |
| -- | --| -- |
| prepend(1+2,[]) = [3] | 1| [2,3] |
| prepend(2+2,[3]) = [4,3] | 2| [3] |
| prepend(3+2,[4,3]) = [5,4,3] | 3| [] |

Finally, we `return [3,4,5]` by `reverse`.

Therefore, in the implementation of `map`, we fold a new array, starting from an empty array, and applying `prepend` to each element of original array.

### filter
As implementing `map`, we can easily write `filter` by using `foldl` as well.

The different between `map` and `filter` is that `map` prepends each `fn(x)` into `acc`, while, `filter` prepends the element only when `fn(x) === true`.

```js
var filter = function(fn, list) {
    return reverse(foldl((acc, x) => (fn(x)) ? prepend(x, acc) : acc; }, EMPTY, list));
};
```
