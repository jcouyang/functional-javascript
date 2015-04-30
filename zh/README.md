# Functional JavaScript

> 本书的更新已牵至 https://blog.oyanglul.us/javascript/functional-javascript.html 

[![Build Status](https://www.gitbook.io/button/status/book/jcouyang/functional-javascript)](https://www.gitbook.io/book/jcouyang/functional-javascript/activity)

这是一本你可能2小时就能看完的小书，但是涵盖了基本所有函数式编程的内容，还包含了一些 ECMAScript 6 定义的函数式新特性, 如箭头函数, 模式匹配等等. 还会介绍函数式一些重要概念在 JavaScript是如何实现即应用, 以及如何以函数式的思想编写 JavaScript 代码.

如果你属于:

![](https://raw.githubusercontent.com/jcouyang/functional-javascript/master/en/images/i%20have%20no%20idea%20what%20im%20doing.jpg)


可能本书并不适合你, 请先移步[Learn JavaScript](https://www.gitbook.io/book/gitbookio/javascript), 但如果函数式对你来说:

![](https://raw.githubusercontent.com/jcouyang/functional-javascript/master/en/images/function inside function.jpg)

那么这本书将会对你会有所帮助.

我选用的 JavaScript 函数式库是 [Eweda](https://rawgit.com/CrossEye/eweda/master/docs/eweda.html)([Ramda](https://ramdajs.com)的最初实现，更遵守函数式教条，但由于 javascript 的栈很容易爆，Ramda的实现要更 pratical 一些而且可以用的产品代码中， 千万不要在产品中用 eweda,这里只用eweda做介绍目的)

> **Comment**[为什么不用 Underscore](http://fr.umio.us/why-ramda/) Short version: 用 underscore 体会不到函数式的精髓。Long version: 请移步第二章

----

> **Note** 由于会介绍 ECMAScript 6 的新特性, 书中很多写法都是 ECMAScript 6 标准, 只能在实现这些 feature 的浏览器(如 Firefox, 请目前参照各浏览器的[实现情况](http://kangax.github.io/compat-table/es6/))里运行. 另外, 大多数的例子源码都会在文章里的 jsbin 链接里.
