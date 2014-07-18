# Monad

这个概念好难解释, 你可以理解为一个 Lazy 或者是状态未知的盒子. 听起来像是[薛定谔猫](http://zh.wikipedia.org/wiki/%E8%96%9B%E5%AE%9A%E8%B0%94%E7%8C%AB)(估计点进去你会更晕了). 其实就是的, 在你打开这个盒子之前, 你是不知道里面的猫处在那种状态.

Monad 这个黑盒子, 里面到底卖的神马药,我们要打开喝了才知道.

等等, 不是说好要解释 Either 的吗, 嗯嗯, 这里就是在解释 Either. 上节说 Either 是一个 Functor, 可以被 fmap over. 怎么这里又说道黑盒子了? 好吧, Monad 其实也是 Functor.

### Either
先来看看 Either 这种类型会干什么事情. [Either ](http://hackage.haskell.org/package/base-4.7.0.0/docs/Data-Either.html#t:Either)表示要不是左边就是右边的值, 因此我们可以用它来表示薛定谔猫, 要不是活着, 要不死了. Either 还有个方法:
either
```
(a -> c) -> (b -> c) -> Either a b -> c
```
想必你已经对箭头`->`非常熟了吧.如果前面几章你都跳过了,我再翻译下好了. 这里表示接收函数`a->c`和函数` b->c`, 再接收一个 Either, 如果 Either 的值在左边,则使用函数映射` a->c`, 若值在右边,则应用第二个函数映射` b->c`.

作为 Monad, 它还必须具备一个方法 '>>='(这个符号好眼熟的说, 看看 haskell 的 logo, 你就知道 Monad 是有多重要), 也就是 bind 方法.

![](http://www.haskell.org/wikistatic/haskellwiki_logo.png)

bind 方法的意思很简单, 就是给这个盒子加一个操作, 比如往盒子在加放射性原子,如果猫活着,就是绿巨猫, 如果猫是死的,那就是绿巨死猫.

```js
Left("cat").bind(cat => 'hulk'+cat)
// => Left "hulkcat"
Right("deadcat").bind(cat => 'hulk' + cat)
// => Right "hulkdeadcat"
```

这有个毛用啊. 表急... 来看个经典例子
### 走钢索

皮尔斯决定要辞掉他的工作改行试着走钢索。他对走钢索蛮在行的，不过仍有个小问题。就是鸟会停在他拿的平衡竿上。他们会飞过来停一小会儿，然后再飞走。这样的情况在两边的鸟的数量一样时并不是个太大的问题。但有时候，所有的鸟都会想要停在同一边，皮尔斯就失去了平衡，就会让他从钢索上掉下去。

![](http://learnyouahaskell-zh-tw.csie.org/img/pierre.png)

我们这边假设两边的鸟差异在三个之内的时候，皮尔斯仍能保持平衡。

#### 一般解法
首先看看不用 Monad 怎么解
```js
eweda.installTo(this);
var landLeft = eweda.curry(function(n, pole){
    return [pole[0]+n, pole[1]];
});
var landRight = eweda.curry(function(n, pole){
    return landLeft(n, eweda.reverse(pole));
});
var result = eweda.pipe(landLeft(1), landRight(1), landLeft(2))([0,0]);
console.log(result);
// => [3, 1]
```

还差一个判断皮尔斯是否掉下来的操作.

```js
var landLeft = eweda.curry(function(n, pole){
    if(pole==='dead') return pole;
    if(Math.abs(pole[0]-pole[1]) > 3)
      return 'dead';
    return [pole[0]+n, pole[1]];
});
var landRight = eweda.curry(function(n, pole){
    if(pole==='dead') return pole;
    return landLeft(n, eweda.reverse(pole));
});
var result = eweda.pipe(landLeft(10), landRight(1), landRight(8))([0,0]);
console.log(result);
// => dead
```

[完整代码](http://jsbin.com/pozim/8/watch?js,console,output)

------
#### 现在来试试用 Either

我们先把皮尔斯放进 Either 盒子里, 这样皮尔斯的状态只有打开 Either 才能看见. 假设 Either Right 是活着, Left 的话皮尔斯挂了.

```js
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
```js
var stillAlive = function(x){
    console.log(x)
}
var dead = function(x){
    console.log('皮尔斯' + x);
}
either(dead, stillAlive, landLeft(2, [0,0]))
```

好吧, 好像有一点点像了, 但是这只落了一次鸟, 如果我要落好几次呢. 这就需要实现 Either 的 >>= bind 方法了, 如果你还记得前面实现的 Functor, 这里非常像 :

```js
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
```js
either = function(left, right, either){
    if(either.constructor.name === 'Right')
        return right(either.value)
    else
        return left(either.value)
}
```

我们来试试工作不工作.

```js
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

### 到底有什么用呢, Monad
我们来总结下两种做法有什么区别:
1. 一般做法每次都会检查查尔斯挂了没挂, 也就是重复获得之前操作的 context
2. Monad 不对异常做处理, 只是不停地往盒子里加操作. 你可以看到对错误的处理推到了最后取值的 either.
2. Monad 互相传递的只是盒子,  而一般写法会把异常往下传如`"dead"`, 这样导致后面的操作都得先判断这个异常.

> **comment** 由于是用 JavaScript, pole 不限定类型, 所以这里单纯的用字符串代表 pole 的异常状态. 但如果换成强类型的 Java, 可能实现就没这么简单了.


看来已经优势已经逐步明显了呢, Monad 里面保留了值的 context, 也就是我们对这个 Monad 可以集中在单独的本次如何操作value, 而不用关心 context.

> 还有一个 Monad 叫做 Maybe, 实际上皮尔斯的🌰用 Maybe 更为合适, 因为 Maybe 有两种状态, 一种是有值 Just, 一种是没东西 Nothing, 可以自己实现试试.
