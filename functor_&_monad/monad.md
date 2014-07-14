# Monad

这个概念好难解释, 你可以理解为一个 Lazy 或者是状态未知的盒子. 听起来像是[薛定谔猫](http://zh.wikipedia.org/wiki/%E8%96%9B%E5%AE%9A%E8%B0%94%E7%8C%AB)(估计点进去你会更晕了). 其实就是的, 在你打开这个盒子之前, 你是不知道里面的猫处在那种状态.

Monad 这个黑盒子, 里面到底卖的神马药,我们要打开喝了才知道.

等等, 不是说好要解释 Either 的吗, 嗯嗯, 这里就是在解释 Either. 上节说 Either 是一个 Functor, 可以被 fmap over. 怎么这里又说道黑盒子了? 好吧, Either 也是 Monad.

### Either
先来看看 Either 这种类型会干什么事情. [Either ](http://hackage.haskell.org/package/base-4.7.0.0/docs/Data-Either.html#t:Either)表示要不是左边就是右边的值, 因此我们可以用它来表示薛定谔猫, 要不是活着, 要不死了. Either 还有几个方法:
1. either
```
(a -> c) -> (b -> c) -> Either a b -> c
```
想必你已经对箭头`->`非常熟了吧.如果前面几章你都跳过了,我再翻译下好了. 这里表示接收函数`a->c`和函数` b->c`, 再接收一个 Either, 如果 Either 的值在左边,则使用函数映射` a->c`, 若值在右边,则应用第二个函数映射` b->c`.

2. isLeft
```
Either a b -> Bool
```
返回真, 如果值在左边.
3. isRight 同理, 与 isLeft 互斥.



```js

```

### Maybe
