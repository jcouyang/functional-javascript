# lambda

Why we need to talk about lambda, aka. λ, at first?

![](http://images.wikia.com/half-life/en/images/archive/3/3a/20120621181904!Lambda_reactor_complex_logo.png)

先来看下[定义](): lambda包括一条变换规则（变量替换）和一条函数定义方式，Lambda演算之通用在于，任何一个可计算函数都能用这种形式来表达和求值。因而，它是等价于图灵机的.

Lambda is a mapping relationship between `x` to `y`. It is equivalent to the anonymous function in most of Functional Programming Languages. The reason of being named as lambda expression is that these functions are always used once or few times, which do not need function name.

![](http://m.memegen.com/eo6ojw.jpg)
![](http://m.memegen.com/1qleyk.jpg)

Anonymous function can be passed into another function as a parameter, or returned as a closure.

However, 匿名函数不是原本的 lambda 算子, because an anonymous function may accept multiple parameters, like: 

```
multiple(x, y) = x*y

```
Simplify to a mapping expression, removing the function name:

```
(x,y) -> x*y
```

Is this a lambda? Negative. Lambda is not just a simple mapping relationship without function name, but also a mapping from only single parameter. 

Precisely speaking, let's look at into the mapping process above:

1. We create a lambda accepting only one parameter:

```
(x) -> (y -> x*y)  // lambda A
```

1. lambda A gives (returns) us another lambda which also accepts one parameter:

```
(y -> x*y)             // lambda B, which is returned from inside of lambda A
```

1. For example, we pass number 5 into lambda A:

```
(5) -> (y -> 5*y)
```

1. Then we get another lambda B1, with number 5 in it:

```
(y -> 5*y)             // lambda B1
```

1. This lambda `y -> 5*y` is a mapping from `y` to `5*y`, if we pass number 4 into lambda B1, we get:

```
4 -> 5*4
```

That is, the anonymous function  `(x,y)->x*y` is the combination of two lambdas, instead of one lambda as it looks like.

The way that a function accepting single parameter returns another function that accepts the second single parameter is called Curring. We will talk about Curring more in Chapter 02.

这里我们先忍一忍, Let's look at lambda in JavaScript at first.
