# Monad

It seems hard to explain the `Monad`, however, you can interpret `Monad` as a `Lazy` or a box with inside status unknown. This hears like [Schrödinger's cat](http://en.wikipedia.org/wiki/Schr%C3%B6dinger%27s_cat) (but you may get more confused after you clicked into this wiki post). Anyway, "Schrödinger's cat" indicates that you have no idea about the status of the cat inside, before you open the box.
.

So, what is the mystery in the black box of Monad? Let's open and taste.

Have we make an agreement for explaining the `Either`? Yes, we are on the right track. Last chapter we said Either is a Functor that can be `fmap over`. Why here we metioned black box (Monad). In fact, Monad is also a Functor.


### Either

Let's look at the usage of `Either`, The [Either](http://hackage.haskell.org/package/base-4.7.0.0/docs/Data-Either.html#t:Either) type represents values with two possibilities: a value of type Either a b is either Left a or Right b.

either
```
(a -> c) -> (b -> c) -> Either a b -> c
```

You may be familiar with arrow notation `->`. But if you have missed previous chapters, I will give you a brief translation. Here it accepts two functions (`a->c` and `b->c`) and an `Either`, if the value of `Either` is on the leftside, it uses function mapping `a-c`, if on the rightside, applying second function mapping `b->c` 

Being a Monad, it should have a method `>>=`, i.e. `Bind` method. (You maybe familiar with this symbol, if you look at the logo of Heskell, you can understand how importance Monad is).

![](http://www.haskell.org/wikistatic/haskellwiki_logo.png)

The meaning of Bind is simple, that is adding a operation on the box. Putting radioactive atom into box, if cat is alive, it is a Hulk-cat, otherwise, it is a Hulk-dead-cat.


```js
Left("cat").bind(cat => 'hulk'+cat)
// => Left "hulkcat"
Right("deadcat").bind(cat => 'hulk' + cat)
// => Right "hulkdeadcat"
```

How can we use it? Don't worry, let's look at [a typical instance](http://learnyouahaskell.com/a-fistful-of-monads#walk-the-line).

### Walk the line

Pierre has decided to take a break from his job at the fish farm and try tightrope walking. He's not that bad at it, but he does have one problem: birds keep landing on his balancing pole! They come and they take a short rest, then take off in search of breadcrumbs. This wouldn't bother him so much if the number of birds on the left side of the pole was always equal to the number of birds on the right side. But sometimes, all the birds decide that they like one side better and so they throw him off balance, which results in an embarrassing tumble for Pierre.

![](http://learnyouahaskell-zh-tw.csie.org/img/pierre.png)

Let's say that he keeps his balance if the number of birds on the left side of the pole and on the right side of the pole is within three. 

#### General Sulotion
Let's look at the sulotion without Moand, 
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

And we need a operation to determine whether Pierre falls or not.

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

[Full source code](http://jsbin.com/pozim/8/watch?js,console,output)

------
#### Let's try using Either

We put Pierre into the box of Either, so that we can know the status of Pierre only after open Either box. Assuming Either Right is alive, Left is dead.

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
Now, after the bird's landing, it returns an `Either`, either alive or dead. So the box opening functin is:
```js
var stillAlive = function(x){
    console.log(x)
}
var dead = function(x){
    console.log('Pierre' + x);
}
either(dead, stillAlive, landLeft(2, [0,0]))
```

All right, we are getting closer. But now there is only time bird's landing, how can we represent multiple times? The answer is we need implement >>=, bind method of Either. If you remember our implementation of Functor, it looks similiar:

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

Oh, right, `either`
```js
either = function(left, right, either){
    if(either.constructor.name === 'Right')
        return right(either.value)
    else
        return left(either.value)
}
```
Let's try it.

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
// => "Pierredead when land 10 became 15,5"
```
[Full source code](http://jsbin.com/giyig/3/watch)

### Usage of Monad

To summerize the differences of using Monad or not:

0. General sulotion checks the status of Pierre each time, that means retrieving previous context repeatedly
0. Monds doesn't handle the Exception, instead, it adds new operations into box. You can see the Exception handling has been brought in the end of `either` value.
0. Monds passes box, while the general sulotion passes Exception like `dead`, that leads each later operation has to check the Exception.

> **comment** Because we use JaveScript, pole accepts any type, so that we use a string to represent the error state of `pole`. But if we turn into strong-typed Java, it maybe hard to implement.

The Advantage seems to get obvious: that is Monad keeps context of value, we can manipulate this value in certain operation, without worry about context

> There is another Monad named `Maybe`. In fact, it's better to use `Maybe` in Pierre's example, because `Maybe` has two status, the one a `Just` containing value, the other is `Nothing` with nothing. You can try it on yourself.

### Using Monad in JavaScript
Do you know there is a new type called [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise#Browser_compatibility) in EcmaScript 6? If you don't, you probably heard the `$.ajax` in jQuery. However, If you haven't seen `promise`, you might not read the return value of it.

```js
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
***/
```

We can notice there are many  types called `Deferred`, let's try to use it.

```js
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
*/
```
See? it returns another same Object, and the `then` accepts a function manipulating each element of Object. this Object is `Promise`. But why we consider it as Monad? Let's rewrite `Walk the line`:

> We use Promise in ES6, not jQuery Deferred. So do write code in Firefox. And ewada can be installed by:
> 
	```
	var ewd = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true;
	            ewd.src = 'https://rawgit.com/CrossEye/eweda/master/eweda.js';
	(document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(ewd);
	eweda.installTo(this);
	```

```js
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

