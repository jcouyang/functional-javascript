# Higher-order function
在 JavaScript 中, 使用高阶函数是非常方便的.
Using the higher-order function is very conviniency 方便 in JavaScript.

### Function as parameter
假设我现在要对一个数组排序, 用我们熟悉的 `sort`
Suppose 假设 we want to sort an array, using the `sort` we are fimiliar with.

```js
[1,3,2,5,4].sort( (x, y) => x - y )
```
// 逆序应该修改成降序，呼唤是打印错误。互换这一段，英文版使用具体的例子。
如果我们要逆序的排序, 把减号左右的`x` 和`y`呼唤,就这么简单, 但如果我是一个对象数组, 要根据对象的` id` 排序:
If we want to sort the array in descending order, we just pass `(x, y) => y - x` simplily 简单. If there is an array containing Objects, and we want to sort by `Object.id`:

```js
[{id:1, name:'one'},
 {id:3, name:'three'},
 {id:2, name:'two'},
 {id:5, name:'five'},
 {id:4, name:'four'}].sort((x,y) => x.id - y.id)
 ```
是不是已经能够感受到高阶函数与匿名函数组合的灵活性.
Have you feel the flexible of higher-order function and anonymous function combination?


###Function as return value
函数的返回值可以不只是值, 同样也可以是一个函数,  来看[Ramda]()内部的一个工具函数` aliasFor`, 他的作用是给函数 E 的一些方法起一些别名:
The return value of a function can not just be a value, but also can be a function. Let's look at the `aliasFor`, a inner utility function in [Ramda](), which is used to give a alias for certain 某些 member in function E.
// 这里用member（成员）来指函数内部的一些方法

> **Note** 听起来很怪不是吗, 函数怎么有方法, 实际上 JavaScript 的function 是一个特殊对象, 试试在 Firefox console 里敲 `console.log.` 是不是看到了一些方法, 但是 `typeof console.log` 是 function

> **Note** Sounds wired? How can a function contain a member 方法? The function in JavaScript, in fact, is a special Object. Try typing `console.log.` in Firefox console, have you noticed some members in 智能提示, whereas, `typeof console.log` is a function.

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
这里有两个 `return`, 一个是 `fn` 返回自己, 另一个是 `aliasFor` 也返回 `fn`, 并且给 `fn` 了几个别名 `fn.is` `fn.are`...
There are two `return`s, the one is that `fn` returns itself, the another is that `aliasFor` also returns `fn` and gives `fn` a few of alias like `fn.is`, `fn.are` ...

// 提问，问什么这个地方E=()=>{}，而非E={}？


什么意思呢? `fn` 返回 `fn`. 很简单就是 `fn() => fn`, 那么`fn()() => fn() => fn`...以此类推, 无论调用 fn 多少次,都最终返回 fn.

What is the meaning of two returns? Returning `fn` from `fn` simply means `fn() => fn`, so that `fn()() => fn() => fn`, etc. No matter how many time the `fn` is invoked, the final return value is still `fn`.

![Imgur](http://i.imgur.com/1qUCC8s.png)

有什么用呢,  由于这里使用了 `fn` 的副作用(side affect) 来干了一些事情`E[newName] = E[oldName]`, 也就是给 E 的方法起一个别名, 因此每次调用 fn 都会给`E` 起一个别名. `aliasFor`最后返回的是 `fn` 自己的一些别名,使得可以 chain 起来更可读一些:

How do we use this? We make `E[newName] = E[oldName]` by using the side affect of `fn`, that is we give a alias for member of E. 因此每次调用 fn 都会给`E` 起一个别名. `aliasFor`最后返回的是 `fn` 自己的一些别名,使得可以 chain 起来更可读一些:

// 这里的reduce, foldl是E中的方法，还是特指array里面的？

```js
aliasFor('reduce').is('reduceLeft).is(foldl)
```

> **Note** 另外, 函数作为返回值的重要应用, 柯里化与闭包, 将会在在后面专门介绍. 我们先来看下以函数作为参数的高阶函数的典型应用.

> **Note** We will detail the importance of function as a return value, Currying, and clousure. Let's look at 典型应用 of higher-order function as passing value.

[method]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function "function"
