# 函数组合 function composition

通过前面的高阶函数,  map, fold 以及柯里化, 其实以及见识到什么是函数组合了. 如 map 就是 由 fold 函数与 reverse 函数组合出来的.

这就是函数式的思想, 不断地用已有函数, 来组合出新的函数.

![](http://turner.faculty.swau.edu/mathematics/materialslibrary/images/composition.jpg)

如图, `(g.f)(x)`就是 `f` 与 `g`
的组合`g(f(x))`, 而前面实现的 map 函数就相当于 `(reverse.fold)(list)`.

### compose

Eweda 提供了非常方便的compose 方法
```js
var gh = E.compose(f, g)
```

说到了函数组合, 柯里化, 我想现在终于可以解释清楚为什么在这里选用 Eweda 而不是 Underscore 了.

如果我现在想要 tasks 列表中所有属性为` completed`为` true` 的元素, 并按照` id` 排序.

underscore 里会这样写:
```js
_(tasks)
    .chain()
    .filter( task => task.completed===true)
    .sortBy( task => task.id).value();
```

这种方式怎么看都不是函数式, 而是以对象为中心, 或者函数式一些, 组合
```js
_.sortBy(_.filter(tasks, task => task.completed===true), task => task.id)
```

恩恩, 看起来不错嘛, 为什么不用 underscore.

而用 Eweda :
```js
E.compose(E.sortBy(task=>task.id), E.filter(task=>task.completed===true))(tasks)
```
好像没什么区别啊? 不就是用了 compose 吗?

区别大了这, 看见` tasks` 是最后当参数传给`E.sort(E.filter(task=>task.completed===true))`的吗? 而不是写死在filter 的参数中. 这意味着在接到需要处理的数据前, 我已经组合好一个新的函数在等待数据, 而不是把数据混杂在中间, 或是保持在一个中间对象中.

好吧如果你还看不出来这样做的好处. 那么来如果我有一个包含几组 tasks的列表 groupedTasks, 我要按类型选出 completed 为 true 并按 id 排序.

underscore:
```js
_.map(groupedTasks, tasks => _.sortBy(_.filter(tasks, task => task.completed===true), task => task.id))

```
不知道你看懂了没, 反正我看不懂.

来看看 Eweda
```js
var completedAndSorted = E.compose(E.sortBy(task=>task.id), E.filter(task=>task.completed===true))
E.map(completedAndSorted)(groupedTasks)
```
看出来思想完全不一样了吧.

由于 Eweda 的函数都是自动柯里化的, 因此可以随意组合, 最终将需要处理的数据扔给组合好的函数. 这是函数式的思想.

![](http://www.moebiusnoodles.com/s/wp-content/uploads/2012/09/ThreeFunctionMachines.jpg)

而 underscore 要么是以对象保持中间数据, 用 chaining 的方式对目标应用各种函数, 要么用函数嵌套函数, 将目标一层层传递下去.

-----
类似 compose, eweda 还有一个方法叫 pipe, pipe 的函数执行方向刚好与 compose 相反. 比如 `pipe(f, g)`, `f`会先执行, 然后结果传给` g`, 是不是让你想起了 bash 的 pipe
```
find / | grep porno
```
没错,他们都是一个意思. 而且这个函数执行的方向更适合人脑编译(可读)一些.
