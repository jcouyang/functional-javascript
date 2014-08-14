# Function Composition

You actually have already seen some Function Composition in previous chapters, like higher-order function, `map`, `fold` and Currying. In that example, `map` is composited from `fold` and `reverse`.

This is the functional conception, compositing new function from the functions we already have.

![](http://turner.faculty.swau.edu/mathematics/materialslibrary/images/composition.jpg)


As shown in the figure above, `(g.f)(x)` is equivalent to `g(f(x))`, which is a function composited from `f` and `g`. In the case of `map`, which implemented in previous section, is equivalent to `(reverse.fold)(list)`.

### compose

Eweda provides us a convenient `compose` function.

```js
var gh = E.compose(f, g)
```

We have talked about composition, currying, so right now I can explain clearly why choosing Ewada over Underscore.js.

Supposing I need to find out all elements where its `completed` equals `true` in a list, and sort them by `id`.

In underscore, you may write some thing like this:
```js
_(tasks)
    .chain()
    .filter( task => task.completed===true)
    .sortBy( task => task.id)
    .value();
```

This code doesn't look like functional, it likes object-oriented, with a bunch of method that return it self.

```js
_.sortBy(_.filter(tasks, task => task.completed===true), task => task.id)
```

Well, it looks better now. But why we didn't choose use underscore, but use Eweda:

```js
E.compose(E.sortBy(task=>task.id), E.filter(task=>task.completed===true))(tasks)
```

It seems that the code doesn't make any difference, it just use `compose`.

However, the big difference here is, in `E.compose`, `tasks` is the final parameter passed into `E.compose()`, while, in `_.sortBy`, `tasks` is a fixed parameter of `_.filter`, which means it's one time only composition. Howerver, `E.compose` has already compose functions before accepting data, so you still have chance to reuse it for other data, or compose it into other function.

If you haven't seen the benefit of this, let's take a look at the example. Suppose we have a list `groupedTasks`  which contains few group of `tasks` lists, what I want to do is to find out the elements of `completed === true`, and sort them by `id` in each group. the data will look simply like this:
```
[
[{completed:false, id:1},{completed:true, id:2}],
[{completed:false, id:4},{completed:true, id:3}]
]
```

In underscore, we write:
```js
_.map(groupedTasks, tasks => _.sortBy(_.filter(tasks, task => task.completed===true), task => task.id))

```
I am not sure whether you can understand it or not, anyway I can not understand it.

While in more functional way, we'd write:
```js
var completedAndSorted = E.compose(E.sortBy(task=>task.id), E.filter(task=>task.completed===true))
E.map(completedAndSorted)(groupedTasks)
// now i got my tasks updated, and I wanna sort it again
groupedTasks = newGroupedTasks
E.map(completedAndSorted)(groupedTasks)
```

Have you notice that the thinking of processing data is totally different.

While we doing function composition, functions are automatic currying, so that they can be composited at will, then we use the composed function to deal with the data. This is functional composition conception.

![](http://www.moebiusnoodles.com/s/wp-content/uploads/2012/09/ThreeFunctionMachines.jpg)

However, in underscore, either we keep intermediate data in Object, applying various functions on target by chaining approach(this is called Flow-Base programming, but I think it's same thing as Monad I'll cover in next chapter), or we use nested functions passing the target through each level.

-----
Similar to `compose`, Eweda has a function called `pipe`, which executes function in opposite direction of `compose`. Like `pipe(f, g)`, Eweda executes `f` at first, then passes result to `g`. Does this remind you of `pipe` in bash?
```
find / | grep porno
```
It's actually `pipe(find, grep(porno))(/)` isn't it.

Yes, they are all the same. And this execution direction is more readable for human brain compiler.

[source code](http://jsbin.com/hivaje/1/edit?js)
