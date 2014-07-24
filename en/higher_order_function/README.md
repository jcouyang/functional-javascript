# Higher-order function
In last chapter, We have talked about the anonymous function and arrow function. So what is the usage of anonymous first-order function? Let's look at the Higher-order function.

A higher-order function accepts another function as a parameter. Why we name that "Higher-order" here? Let's look at this function: `f(x, y) = x(y)`, according to the simplifying process of lambda, we have:
```
f(x) => (y -> x(y))
(y) => x(y)
```
// 此处是否写成这样更合理： f只是一个别名（variable），相当于
var f = x => (y => x(y))
y => x(y)

可以卡出来虽然只是调用 f, 但是返回还有一个函数x要求值.
We can notice that the function `f` need another function `x` as parameter, although `f` is the only function we invoked.
// f这个例子中，最后不是返回一个函数x，x(y)已经计算出值了啊，应该重点强调“高阶函数意思是它接收另一个函数作为参数”，呼应上文

还记得高等数学里面的导数吗, 两阶以上的导数叫高阶导数. 因为求导一次以后返回的可以求导.
Have you remembered the derivative of Advanced Mathematics, where we call higher derivative, because we can calculate derivation on 一阶导数

Thay are all share similiar conception, like [Matryoshka doll](https://en.wikipedia.org/wiki/Matryoshka_doll). The reason it is called high-order function is that it 
// “当函数执行以后还需执行或者要对参数执行”，有点没看明白
当函数执行以后还需执行或者要对参数执行, 因此叫高阶函数.![](http://s3.amazonaws.com/lyah/recursion.png)

There are many typical usage of higher-order function such as `map` or `reduce`. They accept different functions to manipulate each element in array in different ways.
 
而柯里化, 则是每次消费一个参数并返回一个逐步被配置好的函数.
Currying, 另一种相似的模式, uses one parameter and returns another function. 
// 能引用维基百科的定义吗？

All features of higher-order function bring us the flexibility to combinate functions. You will find the powerful usage of function combination at the end of this chapter.