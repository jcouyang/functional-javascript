-   [JavaScript玩转Clojure大法之 - 并发编程](./clojure-core.async-essence-in-native-javascript.org)
-   **[JavaScript玩转Clojure大法之 - Transducer](./clojure-essence-in-javascript-transducer.org)**
-   [JavaScript玩转Clojure大法之 - Trampoline](./clojure-essence-in-javascript-trampoline.org)
-   [JavaScript玩转Clojure大法之 - Macro (1)](./clojure-essence-in-javascript-macro.org)

通过上一篇[Clojure风格的JavaScript并发编程](./clojure-core.async-essence-in-native-javascript.org)介绍了如何用JavaScript享受到Clojure在并发编程的优势. 我决定 写一系列关于如何用JavaScript玩转Clojure大法的文章. 这回要用JavaScript玩转另一个 Clojure全新的概念 -- *Transducer*.

Transducer 是 Rich Hickey[1] [高调宣布](http://blog.cognitect.com/blog/2014/8/6/transducers-are-coming) 的在Clojure 1.7 版本加入的又一大法. 在之前的另一个概念 [Reducer](http://clojure.com/blog/2012/05/15/anatomy-of-reducer.html) 却没那么 **高调**. 在解释transducer之前, 先看看什么是Reducer, 如果能看懂, 再接着看Transducer.

Reducer
=======

说道reduce这个词, 想必JS Developer大多会用过underscore[2](或类似)的reduce方法, 大概形式是这样

``` javascript
  _.reduce(fn, 0, [1,2,3])
```

大概意思是初始为0, 应用fn到每一个collection(检测coll)元素上,得到一个新的值.

如果加上map, 比如(我要开始用[mori](https://github.com/swannodette/mori)[3] 了)

``` javascript
  reduce(sum, 0, map(inc [1,2,3]))
```

> Terminology:
> 1.  reducing 函数: 用来reduce的函数, 比如sum
> 2.  transform: 变换, 从一个函数变另一个函数
> 3.  xf: xform, transform 函数
> 4.  reducible: 可被reduce的,也就是实现reduce接口的,比如所有的collection

让我们一步一步分析一下这次reduce到底干了什么
1.  map 函数 inc 到 coll 每一个元素, 得到一个新的 coll `[2,3,4]`
2.  reduce 把新coll的每个元素用sum函数, 得到一个新的值.

好吧这就是reduce了, 用一个reducing函数sum去计算coll得出一个新的值.

来看看更好的解法

transform
---------

reduce函数需要等待map返回新的coll后才能reduce, 那么可不可以一步直接算出来呢?

假如我们有一个函数xf可以变换reducing函数(上例的sum是reducing函数)的形式, 比如

``` example
xf(reduceFn) -> anotherReduceFn
```

再假如我们的新map函数可以做这种转换

``` javascript
  map(inc)(sum) -> aShinyNewReduceFn
```

> map 函数的简单transform实现可以这样实现,如果你感兴趣的话
>
> ``` javascript
>   function map(fn){
>     return function(reduceFn){
>       return function(result, input){
>         reduceFn(result, fn(input))
>       }
>     }
>   }
> ```

那么我们之前的reduce就可以写成

``` javascript
  reduce(map(inc)(sum),0,[1,2,3])
```

yeah, 现在只需要一步就reduce出来结果了, reduce应用 `map(inc)(sum)` 来计算值, 只需要遍历一遍coll

[Reducer](http://clojure.org/reducers)
--------------------------------------

但是如果我们不想改变map函数的接口, 原始形式的接口还是比较好写好读的

``` javascript
  reduce(sum, 0, map(inc [1,2,3]))
```

那么需要进一步的抽象, 我把新的map函数叫做rmap好了

``` javascript
  function rmap(fn, coll){
    reducer(coll, map(fn))
  }
```

跟以前接口一样,接收函数和coll,但是返回一个由reducer生成的reducible, 所以就变成了

``` javascript
  reduce(sum, 0, reducer([1,2,3], map(inc)))
```

等等,怎么做到的...你已经消费了coll了, 那reducing函数怎么进来的, reducer怎么知道用sum去reduce呢.

### Reducible

答案是, 反转reduce的关系, 原来reduce用sum去计算结果, 现在,我们调用reducible的reduce方法来计算结果

![](./images/came-out.gif)

如果你还没有被我弄晕的话, 准备好, 又来一个新单词 *reducible*. 也就是可以被reduce的东西.

于是我们需要coll实现reduce方法,这样就成为reducible了.

也就是reduce函数现在应该长这样, 我们暂且叫它 `rreduce`

``` javascript
  function rreduce(reduceFn, init, reducible){
    reducible(reduceFn, init)
  }
```

那么我们的例子就变成了这样

``` javascript
  reducer([1,2,3], map(inc))(sum, 0)
```

reducer接收coll和xf, 返回reducible函数. 这一切都是lazy的, 直到rreduce调用*第(coll)行*才执行.

``` javascript
  function reducer(coll, xf){
    return function(reduceFn, init){
      return coll.reduce(xf(reduceFn), init) (ref:coll)
    }
  }
```

Transducer
==========

说了半天Reducer,明明说好的要解释的Transducer呢?

如果你还能follow, 那么现在要开始解释Transducer了

其实你已经见过Transducer了, 再回顾一下之前说的Reducer
1.  接收一个xf函数和一个coll
2.  用xf转换reducing函数, 并应用到coll

Transducer就是那个xf

``` javascript
  reduce(map(inc)(sum),0,[1,2,3])
```

也就是这里面的 `map(inc)`

靠, 就这么简单?

就是这么简单, 前面说了reducer的出现是因为想保持原始reduce的api不便, 那么tranducer则提供了 另外一种reduce api

``` javascript
  transduce(map(inc), sum, 0, [1,2,3])
```

transduce接收一个transducer,一个reducing function, 一个初始值, 一个coll. 这段代码跟前面干的事情一模一样.

另外牛逼的是transducer跟context完全没有关系, 就是完全与数据解耦开来, 比如我们组装好一个transducer xf

可以用在任何地方

``` javascript
  seq(xf data) //生成一个lazy的序列, 同时lazy transform, 每次取的时候data会被transform
  into([], xf data) //把 data transform后放到一个数组里
  chan(1, xform) // 当数据经过CSP的channel时被transform
```

Is it Curry?
============

怎么看着有点像柯里化, 一样么?

当然不是, 柯里化或者部分参数只是部分配置参数, 而transducer是一次多n次转换的组合

比如一个柯里化的map可以

``` javascript
  var mapinc = map(inc)
  mapinc([1,2,3])
```

而不能

``` javascript
  mapinc(sum)
```

因为map就俩参数, 第一个是函数第二个是data, 如果再给data会错误

但是tranceducer只是转换, 所以只接受reducing函数

``` javascript
  reduce(mapinc(sum), 0, [1,2,3])
  // => 9
```

完整例子
========

[1] Clojure的作者

[2] 我是故意吧reduce的参数顺序写"反"的, 原来underscore是先消费collection的. 至于为什么要反过来 可以参考[这个解释](http://blog.oyanglul.us/javascript/functional-javascript.html#sec-3-2)

[3] clojurescript作者把clojurescript的一些数据结构和函数编译成javascript, 这样就可以用普通js使用 clojure中的数据结构和函数. document严重过时, 建议看导入的[源代码](https://github.com/swannodette/mori/blob/master/src/mori.cljs), 以及clojure的文档, 接口和clojure基本一致.
