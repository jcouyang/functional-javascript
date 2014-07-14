# Monad

这个概念好难解释, 你可以理解为一个 Lazy 或者是状态未知的盒子. 听起来像是[薛定谔猫](http://zh.wikipedia.org/wiki/%E8%96%9B%E5%AE%9A%E8%B0%94%E7%8C%AB)(估计点进去你会更晕了). 其实就是的, 在你打开这个盒子之前, 你是不知道里面的猫处在那种状态.

Monad 这个黑盒子, 里面到底卖的神马药,我们要打开喝了才知道.

等等, 不是说好要解释 Either 的吗, 嗯嗯, 这里就是在解释 Either. 上节说 Either 是一个 Functor, 可以被 fmap over. 怎么这里又说道黑盒子了? 好吧, Either 也是 Monad.

### Either
先来看看 Either 这种类型会干什么事情. [Either ](http://hackage.haskell.org/package/base-4.7.0.0/docs/Data-Either.html#t:Either)表示要不是左边就是右边的值, 因此我们可以用它来表示薛定谔猫, 要不是活着, 要不死了. Either 还有个方法:
either
```
(a -> c) -> (b -> c) -> Either a b -> c
```
想必你已经对箭头`->`非常熟了吧.如果前面几章你都跳过了,我再翻译下好了. 这里表示接收函数`a->c`和函数` b->c`, 再接收一个 Either, 如果 Either 的值在左边,则使用函数映射` a->c`, 若值在右边,则应用第二个函数映射` b->c`.

作为 Monad, 它还必须具备一个方法 '>>='(这个符号好眼熟的说), 也就是 bind 方法.

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

首先看看不用 Monad 怎么解
```js
eweda.installTo(this);
var landLeft = eweda.curry(function(n, pole){
    return [pole[0]+n, pole[1]];
});
var landRight = eweda.curry(function(n, pole){
    return [pole[0], pole[1]+n];
});
var result = eweda.pipe(landLeft(1), landRight(1), landLeft(2))([0,0]);
console.log(result);
// => [3, 1]
```

还差一个判断皮尔斯是否掉下来的操作.

```js
var landLeft = eweda.curry(function(n, pole){
    if(pole==='dead' || Math.abs(pole[0]-pole[1]) > 3)
      return 'dead';
    return [pole[0]+n, pole[1]];
});
var landRight = eweda.curry(function(n, pole){
    if(pole==='dead') return pole;
    landLeft(n, eweda.reverse(pole))
});
var result = eweda.pipe(landLeft(1), landRight(1), landLeft(2))([0,0]);
console.log(result);

```

