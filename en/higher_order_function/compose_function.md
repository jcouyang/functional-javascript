# Function Composition

We have seen the Function Composition in our previous chapters, like higher-order function, `map`, `fold` and Currying. In fact, `map` is composited from `fold` and `reverse`.

This is the functional conception, compositing new function from the functions we already have.

![](http://turner.faculty.swau.edu/mathematics/materialslibrary/images/composition.jpg)


As shown in the figure above, `(g.f)(x)` is equivalent to `g(f(x))` composited from `f` and `g`. Similarly, `map` implamented in previous section is equivalent to `(reverse.fold)(list)`.

### compose

Eweda provides us a convenient `compose` function.

```js
var gh = E.compose(f, g)
```

We have talked about composition, currying, so right now I can explain clearly why choosing Ewada instead of Underscore.js.

Supposing I need find out all elements where its `completed` equals `true` in a list, and sort them by `id`.

In underscore, I will write:
```js
_(tasks)
    .chain()
    .filter( task => task.completed===true)
    .sortBy( task => task.id).value();
```

这种方式怎么看都不是函数式, 而是以对象为中心, 或者函数式一些, 组合
This code doesn't look like functional, it likes object-oriented, 或者函数式一些, 组合
```js
_.sortBy(_.filter(tasks, task => task.completed===true), task => task.id)
```

Well, it looks better now. But why we don't use underscore, and we use Eweda:

```js
E.compose(E.sortBy(task=>task.id), E.filter(task=>task.completed===true))(tasks)
```

It seems the code doesn't make any difference, it just use `compose`.

However, the big difference here is, in `E.compose`, `tasks` is the final parameter passed into `E.sort(E.filter(task=>task.completed===true))`, whereas, in `_.sortBy`, `tasks` is a binded parameter of `_.filter`. That means, `E.compose` has already created a new function before accepting the parameter, instead of mixing data (passed as parameter) during composition, or keeping data in a middle object like `_.filter`.

If you haven't seen the benefit of this, let me give you a expamle. Supposing I have a list `groupedTasks` containing a few of `tasks`, I want to find out the elements of `completed === true`, and sort them by `id`.

In underscore, we write:
```js
_.map(groupedTasks, tasks => _.sortBy(_.filter(tasks, task => task.completed===true), task => task.id))

```
I am not sure whether you can understand it or not, anyway I can not understand it.

While in Eweda, we write:
```js
var completedAndSorted = E.compose(E.sortBy(task=>task.id), E.filter(task=>task.completed===true))
E.map(completedAndSorted)(groupedTasks)
```

Have you notice that the thought is totally different.

In Eweda, functions are automatic currying, so that they can be composited at will, then create a new function accepting the data that needs to be handled. This is functional conception.

![](http://www.moebiusnoodles.com/s/wp-content/uploads/2012/09/ThreeFunctionMachines.jpg)

However, in underscore, either we keep intermediate data in Object, applying various functions on target by chaining approach, or we use nested functions passing the target through each level.

-----
Similar to `compose`, Eweda has a function called `pipe`, which executes function in opposite direction of `compose`. Like `pipe(f, g)`, Eweda executes `f` at first, then passes result to `g`. Does this remind you of `pipe` in bash?

```
find / | grep porno
```

Yes, they are all the same. And this execution direction is more suitable for compiling in the human brain (more readable).
