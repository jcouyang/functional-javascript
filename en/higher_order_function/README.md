# Higher-order function
In previous chapter, We have talked about the anonymous function and arrow function. So what can we do with such kind of anonymous first-classs function? Let's check out Higher-order function.

A higher-order function is function that accepts another function as a parameter. Why we name that "Higher-order" here? Let's look at this function: `f(x, y) = x(y)`, according to the simplifying process of lambda, we have:
```
var f = x => (y => x(y))
y => x(y)
```

We can notice that the function `f` need another function `x` as parameter, although `f` is the only function we invoked.

Have you remembered the derivative from Calculus, where we call higher derivative, when we calculate derivation on previous derivation.

Thay are all share similar conception, like [Matryoshka doll](https://en.wikipedia.org/wiki/Matryoshka_doll). The reason why it is called high-order function is that it's return value can be invoked again or need to invode some parameter passed in. in.![](http://s3.amazonaws.com/lyah/recursion.png)

There are many typical usage of higher-order function such as `map` or `reduce`. They accept different functions to manipulate each element in array in different ways.

While, Currying is the technique of returning a partial configured function each time it consume a argument.

All features of higher-order function bring us the flexibility to compose functions. You will feel what a powerful technique compose function is by the end of this chapter.
