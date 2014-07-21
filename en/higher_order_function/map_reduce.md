# map fold filter

We will talk about the classic usage of higher-order function: `map`, `fold/reduce` (in [my](https://github.com/jcouyang) opinion, the name fold is more vivid than the name reduce).

![](http://www.backpacker.com/2007/images/september2010/mapfolding_200x170.png)

`map` applies a specific function on each element of list, then returns a new list.

`fold` folds all of the elements, and finally returns a single value, a single array or a single Object. We have two types of `fold`: `foldl` folds elements from left to right, while, `foldr` folds elements from right to left.

`filter` filters elements accroding to certain condition, as its name means.

### fold left
Let's look at how we implement and use the `foldl`, and we will find the reason of implementing `foldl` initially.

Here is the code of implemention in [Eweda](https://rawgit.com/CrossEye/eweda/master/docs/eweda.html):
```js
var foldl = function(fn, acc, list) {
    return (isEmpty(list)) ? acc : foldl(fn, fn(acc, head(list)), tail(list));
};
aliasFor("foldl").is("reduce");
```

As the code represents, `foldl` accepts three parameters: 

* a function `fn` accepting two parameters: `acc` and element of list, 
* an accumulated value `acc`, 
* and the `list` we need apply `fold` on.

This implementation is a simple recursion, because `foldl` invokes itself.

0. The terminal condition of recursion is `isEmpty(list) === true`, then it returns `acc`,
0. otherwise, it applies `fn` on `acc` and the first element of list called `head(list)`, and passes the result returned from `fn` as new `acc` into `foldl`,
0. then, it passes the remaining list called `tail(list)` as new list into `foldl`


Futhermore, this is a **tail recursion**.

> **Note**Why we use tail recursion?

0. We will get the result of each calculation for `fn(acc, head(list)`, and pass the result into next invocation,
0. so that, we can get the final result directly at the end of recursion.

Right now, we have a working 工作的 `foldl`, let's look at how easy we implement the `foldr`.
```js
var foldr = function(fn, acc, list) {
    return (isEmpty(list)) ? acc : fn(head(list), foldr(fn, acc, tail(list)));
}
```

`foldr` is not a tail recursion, and we can find the different between non-tail recursion and tail recursion.

Supposing we want to fold `[1,2,3]` with addition, we fold the list as following steps:
```js
foldl((a, b) => a+b, 0, [1,2,3])
```
| acc |head| tail |
| -- | --| -- |
| (0,1) => 0+1 = 1 | 1| [2,3] |
| (1,2) => 1+2 = 3 | 2| [3] |
| (3,3) => 3+3 = 6 | 3| [] |

Let's turn to the steps of `foldr`:
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

Is that clear for the different between non-tail recursion and tail recursion? Tail recursion returns final result just as it has finished the last calulation. Non-tail recursion has to get the result from `foldr(fn, 0, [])`, and passes result to each outer invocation. (Have you noticed that each group of parentheses is one level of recusion, ie. the depth of stack)

### map
Look at the implementaion of `map`:
```js
var map = function(fn, list) {
    return reverse(foldl((acc, x) => prepend(fn(x), acc), [], list));
};
```

Why we use `foldl`? Let's explain it:
```js
map(a => a+2, [1,2,3])
```
| acc |head|tail |
| -- | --| -- |
| prepend(1+2,[]) = [3] | 1| [2,3] |
| prepend(2+2,[3]) = [4,3] | 2| [3] |
| prepend(3+2,[4,3]) = [5,4,3] | 3| [] |

Finally, we `return [3,4,5]` by `reverse`.

Therefore, in the implemetation of `map`, we fold a new array, starting from an empty array, and applying `prepend` to each element of original array.

### filter
As implementing `map`, we can easily write `filter` by using `foldl`.

The different between `map` and `filter` is that `map` prepands each `fn(x)` into `acc`, while, `filter` prepands the element only when `fn(x) === true`.

```js
var filter = function(fn, list) {
    return reverse(foldl((acc, x) => (fn(x)) ? prepend(x, acc) : acc; }, EMPTY, list));
};
```
