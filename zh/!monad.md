Functor
=======

Functor
-------

Functor 是 可以被 map over 的类型. 什么叫 map over...

比如 list 就可以说是可以被map over... 那么是不是可枚举类型?

不是的, 来看看 Haskell 中如何解释(其实所有函数式的概念可能用 haskell 是最能说明问题的了).

``` haskell
  ghci > :t fmap
  fmap :: (a -> b) -> fa -> f b
```

`fmap` 又是什么东西, fmap 是 map over Functor 的函数. 这个函数只干一个事情, 可能通过前面解释的一点点 Haskell功夫,你可能能翻译 `(a -> b) -> fa -> f b` 了把. 给定一个从 `a` 到 `b` 的映射函数, 再给定一个 a 的 Functor, 返回一个 b 的 Functor.

虽然个个字都认识, 但怎么就不知道啥意思.

如果我再说一个新词, 你是不是会疯掉了-- Lift.

好吧, 把他们都串起来, 你就明白了.
1.  平常我们可以把 `a` 到 `b` 的映射可以叫做 map, 映射的方式就是函数了.
2.  那么类似的对于函数或者其他可以做这种 map 操作的类型或一种计算方式, 叫做 Functor.
3.  而这种 map 就叫做 fmap, 给定 a 集合到 b 集合的映射方式(也就是一个函数), 就能找到 对 a 的一种计算(computation, 任何可变换的类型, 这就是 Functor) 的变换 -- 对 b 的对应计算方式.
4.  如果该计算是一个函数, 那么这个操作叫做 lifting. 非常形象的, 根据 a 到 b 的映射 lift(举) 到另一个层面上.

![](http://learnyouahaskell-zh-tw.csie.org/img/lifter.png)

虽然 lifting 很形象, 但是还是越说越抽象了, 来举个栗子.

举个栗子🌰
---------

> 注意我们还没有实现 Functor, 因此下面的栗子还不能运行在你的 console.

前面说了, Functor 可以是数组, 因为数组可以被 map over

``` javascript
  var plus1 = n => n+1;
  fmap(plus1, [2, 4, 6, 8])// => [3,5,7,9]
```

这里,数组 Array 就是 Functor 类型, 而 fmap 把 2 -&gt; 3 的映射方式对 Array \[2,4,6,8\] 进行了变换, 得到 \[3,5,7,9\]. 这跟数组的 map 方法一样, 比较好理解.

再试试换一种 Functor 类型, 试试函数

``` javascript
  var times2 = m => m*2;
  fmap(plus1, times2) // => function(){}
  fmap(plus1, times2)(3) // => 7 (3*2+1)
```

看到 fmap 返回的是一个函数, 因为你 map over 的是一个函数 `times2`. 还记得 `(a -> b) -> f a -> f b` 的公式么, 因为现在的 Functor 为 Function 类型, 我们可以把=f=替换成函数也就是 x 到 y 的映射, 因此我们可以将该公式替换为

``` example
(a -> b) -> (x -> a) -> (x -> b)
```

再用我们具体的函数 plus1 替换进去

``` example
(n->n*2) -> plus1(n) -> plus1(n*2)
```

也就是说, 这个 fmap 会把函数 times2 应用到 plus1 的任何结果上.

这不就是函数组合吗 `plus1(times2(3))`, 确实是的. 但这只是 Functor 的冰山一角, dan在来看看别的Functor

Functor 还可以是别的东西...比如

``` example
fmap(plus1, Either(10, 20))
```

Either也是 Functor, 慢着, Either 是什么类型, 好吧,在解释 Either 之前, 我们先忍一忍, 来先看看 JavaScript 中怎么实现以及使用一个 Functor.

Functor in JavaScript
---------------------

首先, 我们用定义一个确定 Functor 类型的函数, 如果没有注册的类型抛出异常.

``` javascript
  var types = function(obj) {
    throw new TypeError("fmap called on unregistered type: " + obj);
  };
```

然后实现注册 Functor 的函数.

``` javascript
   Functor = function(type, defs) {
          var oldTypes = types;
          types = (obj) => {
              if (type.prototype.isPrototypeOf(obj)) {
                  return defs; // 这是递归的出口, 判断类型, 确定 fmap 的 Functor 实例属于注册的哪一个 Functor
              }
              return oldTypes(obj); //不断递归寻找 types, 这个效率会很低, 因为调用栈上好多闭包, 每个闭包都保持着 type 和 defs
          }
  };
```

这样可以用 Functor 函数注册一个新的 Functor 类型并定义它自己的 fmap 方法(还记得前面说的 Functor 只有一个方法吗). 比如我们要把 Array 变成 Functor

``` javascript
  Functor(Array, {
      fmap: (fn, array) => {
          arr.map(x => fn(x))
      }
  })
```

好像快要完成的样子. 现在还差 fmap Functor 类型函数了. 这个函数干两件事情, 找到实例属于哪个 Functor 类型, 并调用他的 fmap 方法.

``` javascript
  fmap = eweda.curry((fn, obj) => {
      return types(obj).fmap(f, obj)
  })
```

同样的, 我们很快可以把 Function 也变成 Functor

``` javascript
  Functor(Function, {
      fmap: (f, g) => {
          return eweda.compose(f, g);
  }})
```

还记得前面说 fmap 函数像函数组合吗, 呵呵, 我们这里就按函数组合实现.

------------------------------------------------------------------------

来总结一下 fmap 和 Functor 到底是什么, fmap 可以将函数应用到 Functor 上, Functor 可以看做是容器或者是带 context 的值. 也就是说如果我们想变换 x 的值, 直接给一个函数映射 `x=> x*2` 即可. 如果我想变换一个数组, 一个函数, 或者 Either 这种带有 context 的或者说容器里面的值, 总不能直接把这些容器直接给函数吧，这时就需要 fmap 将函数的映射关系应用到容器里面的值. 其实就是打开，调一下函数，完了再包好。

好吧, 通过如何实现和使用一个简单的 Functor, 概念上已经估计可以理解了, 我们回过头来看看 Either 是神马玩意.

[完整代码](http://jsbin.com/xezun/1/embed?js,console)

Monad
=====

这个概念好难解释, 你可以理解为一个 Lazy 或者是状态未知的盒子. 听起来像是[薛定谔猫](http://zh.wikipedia.org/wiki/%E8%96%9B%E5%AE%9A%E8%B0%94%E7%8C%AB)(估计点进去你会更晕了). 其实就是的, 在你打开这个盒子之前, 你是不知道里面的猫处在那种状态.

Monad 这个黑盒子, 里面到底卖的神马药,我们要打开喝了才知道.

等等, 不是说好要解释 Either 的吗, 嗯嗯, 这里就是在解释 Either. 上节说 Either 是一个 Functor, 可以被 fmap over. 怎么这里又说道黑盒子了? 好吧, Monad 其实也是 Functor. 还记得我说的 Functor 其实是一个带 context 的盒子吗. 而 fmap 使得往盒子里应用函数变换成为了可能.

Either
------

先来看看 Either 这种类型会干什么事情. [Either](http://hackage.haskell.org/package/base-4.7.0.0/docs/Data-Either.html#t:Either)表示要不是左边就是右边的值, 因此我们可以用它来表示薛定谔猫, 要不是活着, 要不死了. Either 还有个方法: either

``` example
(a -> c) -> (b -> c) -> Either a b -> c
```

想必你已经对箭头 `->` 非常熟了吧.如果前面几章你都跳过了,我再翻译下好了. 这里表示接收函数 `a->c` 和函数 `b->c`, 再接收一个 Either, 如果 Either 的值在左边,则使用函数映射 `a->c`, 若值在右边,则应用第二个函数映射 `b->c`.

作为 Monad, 它还必须具备一个方法 '&gt;&gt;='(这个符号好眼熟的说, 看看 haskell 的 logo, 你就知道 Monad 是有多重要), 也就是 bind 方法.

![](http://www.haskell.org/wikistatic/haskellwiki_logo.png)

bind 方法的意思很简单, 就是给这个盒子加一个操作, 比如往盒子在加放射性原子,如果猫活着,就是绿巨猫, 如果猫是死的,那就是绿巨死猫.

``` javascript
  Left("cat").bind(cat => Right('hulk'+cat))
  // => Left "hulkcat"
  Right("deadcat").bind(cat => Left('hulk' + cat))
  // => Right "hulkdeadcat"
```

这有个毛用啊. 表急... 来看个经典例子

走钢索
------

皮尔斯决定要辞掉他的工作改行试着走钢索。他对走钢索蛮在行的，不过仍有个小问题。就是鸟会停在他拿的平衡竿上。他们会飞过来停一小会儿，然后再飞走。这样的情况在两边的鸟的数量一样时并不是个太大的问题。但有时候，所有的鸟都会想要停在同一边，皮尔斯就失去了平衡，就会让他从钢索上掉下去。

![](http://learnyouahaskell-zh-tw.csie.org/img/pierre.png)

我们这边假设两边的鸟差异在三个之内的时候，皮尔斯仍能保持平衡。

### 一般解法

首先看看不用 Monad 怎么解

``` javascript
  eweda.installTo(this);
  var landLeft = eweda.curry(function(n, pole){
      return [pole[0]+n, pole[1]];
  });
  var landRight = eweda.curry(function(n, pole){
      return eweda.reverse(landLeft(n, eweda.reverse(pole)));
  });
  var result = eweda.pipe(landLeft(1), landRight(1), landLeft(2))([0,0]);
  console.log(result);
  // => [3, 1]
```

还差一个判断皮尔斯是否掉下来的操作.

``` javascript
  var landLeft = eweda.curry(function(n, pole){
      if(pole==='dead') return pole;
      if(Math.abs(pole[0]-pole[1]) > 3)
        return 'dead';
      return [pole[0]+n, pole[1]];
  });
  var landRight = eweda.curry(function(n, pole){
      if(pole==='dead') return pole;
      return eweda.reverse(landLeft(n, eweda.reverse(pole)));
  });
  var result = eweda.pipe(landLeft(10), landRight(1), landRight(8))([0,0]);
  console.log(result);
  // => dead
```

[完整代码](http://jsbin.com/pozim/8/watch?js,console,output)

------------------------------------------------------------------------

### 现在来试试用 Either

我们先把皮尔斯放进 Either 盒子里, 这样皮尔斯的状态只有打开 Either 才能看见. 假设 Either Right 是活着, Left 的话皮尔斯挂了.

``` javascript
  var land = eweda.curry(function(lr, n, pole){
      pole[lr] = pole[lr] + n;
      if(Math.abs(pole[0]-pole[1]) > 3) {
        return new Left("dead when land " + n + " became " + pole);
      }
      return new Right(pole);
  });

  var landLeft = land(0)
  var landRight = land(1);
```

现在落鸟后会返回一个 Either, 要不活着, 要不挂了. 打开盒子的函数可以是这样的

``` javascript
  var stillAlive = function(x){
      console.log(x)
  }
  var dead = function(x){
      console.log('皮尔斯' + x);
  }
  either(dead, stillAlive, landLeft(2, [0,0]))
```

好吧, 好像有一点点像了, 但是这只落了一次鸟, 如果我要落好几次呢. 这就需要实现 Either 的 &gt;&gt;= bind 方法了, 如果你还记得前面实现的 Functor, 这里非常像 :

``` javascript
  var Monad = function(type, defs) {
    for (name in defs){
      type.prototype[name] = defs[name];
    }
    return type;
  };
  function Left(value){
    this.value = value
  }
  function Right(value){
    this.value=value;
  }

  Monad(Right, {
    bind:function(fn){
      return fn(this.value)
    }
  })

  Monad(Left, {
    bind: function(fn){
      return this;
    }
  })
```

哦, 对了, either:

``` javascript
  either = function(left, right, either){
      if(either.constructor.name === 'Right')
          return right(either.value)
      else
          return left(either.value)
  }
```

我们来试试工作不工作.

``` javascript
  var walkInLine = new Right([0,0]);
  eitherDeadOrNot = walkInLine.bind(landLeft(2))
      .bind(landRight(5))
  either(dead, stillAlive, eitherDeadOrNot)
  // => [2,5]
  eitherDeadOrNot = walkInLine.bind(landLeft(2))
    .bind(landRight(5))
    .bind(landLeft(3))
    .bind(landLeft(10)
    .bind(landRight(10)))

  either(dead, stillAlive, eitherDeadOrNot)
  // => "皮尔斯dead when land 10 became 15,5"
```

[完整代码](http://jsbin.com/giyig/3/watch)

到底有什么用呢, Monad
---------------------

我们来总结下两种做法有什么区别:

1.  一般做法每次都会检查查尔斯挂了没挂, 也就是重复获得之前操作的 context
2.  Monad 不对异常做处理, 只是不停地往盒子里加操作. 你可以看到对错误的处理推到了最后取值的 either.
3.  Monad 互相传递的只是盒子, 而一般写法会把异常往下传如 `dead`, 这样导致后面的操作都得先判断这个异常.

> 由于是用 JavaScript, pole 不限定类型, 所以这里单纯的用字符串代表 pole 的异常状态. 但如果换成强类型的 Java, 可能实现就没这么简单了.

看来已经优势已经逐步明显了呢, Monad 里面保留了值的 context, 也就是我们对这个 Monad 可以集中在单独的本次如何操作value, 而不用关心 context.

> 还有一个 Monad 叫做 Maybe, 实际上皮尔斯的🌰用 Maybe 更为合适, 因为 Maybe 有两种状态, 一种是有值 Just, 一种是没东西 Nothing, 可以自己实现试试.

Monad 在 JavaScript 中的应用
----------------------------

你知道 ES6有个新的 类型 [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise#Browser_compatibility) 吗, 如果不知道, 想必也听过 jQuery 的 `$.ajax` 吧, 但如果你没听过 promise, 说明你没有认真看过他的返回值:

``` javascript
  var aPromise = $.ajax({
      url: "https://api.github.com/users/jcouyang/gists"
      dataType: 'jsonp'
      })
  aPromise /***
  => Object { state: .Deferred/r.state(),
      always: .Deferred/r.always(),
      then: .Deferred/r.then(),
      promise: .Deferred/r.promise(),
      pipe: .Deferred/r.then(),
      done: b.Callbacks/p.add(),
      fail: b.Callbacks/p.add(),
      progress: b.Callbacks/p.add() }
  ,***/
```

我们看到返回了好多 `Deferred` 类型的玩意, 我们来试试这玩意有什么用

``` javascript
  anotherPromise = aPromise.then(_ => _.data.forEach(y=> console.log(y.description)))
  /* =>
  Object { state: .Deferred/r.state(),
      always: .Deferred/r.always(),
      then: .Deferred/r.then(),
      promise: .Deferred/r.promise(),
      pipe: .Deferred/r.then(),
      done: b.Callbacks/p.add(),
      fail: b.Callbacks/p.add(),
      progress: b.Callbacks/p.add() }

  "connect cisco anyconnect in terminal"
  "为什么要柯里化（curry）"
  "批量获取人人影视下载链接"
  ......
  ,*/
```

看见没有, 他又返回了同样一个东西, 而且传给 then 的函数可以操作这个对象里面的值. 这个对象其实就是 Promise 了. 为什么说这是 Monad 呢, 来试试再写一次 `走钢丝`:

> 这里我们用的是 ES6 的 Promise, 而不用 jQuery Defered, 记得用 firefox 哦. 另外 eweda 可以这样装

``` example
var ewd = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true;
            ewd.src = 'https://rawgit.com/CrossEye/eweda/master/eweda.js';
(document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(ewd);
eweda.installTo(this);
```

``` javascript
  var land = eweda.curry(function(lr, n, pole){
      pole[lr] = pole[lr] + n;
      if(Math.abs(pole[0]-pole[1]) > 3) {
        return new Promise((resovle,reject)=>reject("dead when land " + n + " became " + pole));
      }
      return new Promise((resolve,reject)=>resolve(pole));
  });

  var landLeft = land(0)
  var landRight = land(1);

  Promise.all([0,0])
  .then(landLeft(2), _=>_)
  .then(landRight(3), _=>_) // => Array [ 2, 3 ]
  .then(landLeft(10), _=>_)
  .then(landRight(10), _=>_)
  .then(_=>console.log(_),_=>console.log(_))
  // => "dead when land 10 became 12,3"
```

这下是不承认 Promise 就是 Monad 了. 原来我们早已在使用这个神秘的 Monad, 再想想 Promise,也没有那么抽象和神秘了.
