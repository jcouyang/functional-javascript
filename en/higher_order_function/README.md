# Higher-order function
我们已经见识到了匿名函数和箭头函数的用法, 匿名的一等函数到底有什么用呢? 来看看高阶函数的应用.
Last chapter, We have talked about the anonymous function and arrow function, what is the usage of anonymous first-order function? Let's look at the Higher-order function.

高阶函数意思是它接收另一个函数作为参数. 为什么叫`高阶`: 来看看这个函数 `f(x, y) = x(y)`，按照 lambda 的简化过程则是：
Higher-order function accepts another function as a parameter. Why we name that "Higher-order"? Let's look at this function: `f(x, y) = x(y)`, according to the simplifying 简化 process:
```
f(x) => (y -> x(y))
(y) => x(y)
```
// 此处是否写成这样更合理： x => y -> x(y)，思考这个例子的合理性

可以卡出来虽然只是调用 f, 但是返回还有一个函数x要求值.

还记得高等数学里面的导数吗, 两阶以上的导数叫高阶导数. 因为求导一次以后返回的可以求导.

概念是一样的, 如同[俄罗斯套娃](https://en.wikipedia.org/wiki/Matryoshka_doll)当函数执行以后还需执行或者要对参数执行, 因此叫高阶函数.![](http://s3.amazonaws.com/lyah/recursion.png)

Thay are all share similiar conception, like [Matryoshka doll](https://en.wikipedia.org/wiki/Matryoshka_doll). 当函数执行以后还需执行或者要对参数执行, 因此叫高阶函数.

高阶函数很多典型的应用如` map`,`reduce`. 他们都是以传入不同的函数来以不同的方式操作数组元素.
There are many classic higher-order function usage like `map`, `reduce`. They accept different functions to mulipulate 操作 element in array in different ways.
 
而柯里化, 则是每次消费一个参数并返回一个逐步被配置好的函数.
Currying, 另一种相似的模式, uses one parameter and returns another function. (查看定义)

高阶函数的这些应用都是为函数的组合提供灵活性. 在本章结束相信你会很好的体会到函数组合的强大之处.
All features 特性 of higher-order function bring us the flexiblebility 灵活性 to combinate functions. You will feel the powerful of function combination at the end of this chapter.
