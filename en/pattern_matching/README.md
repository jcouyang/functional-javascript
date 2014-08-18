# Pattern Matching
![](http://reasontostand.org/wp-content/uploads/2010/12/matchingshapes_2.jpg)

Pattern Matching means you can define a pattern, like the holes of building blocks above, and programming language will assign the data (blocks) of corresponding sharp into matching slot.

### Destructuring
If you have ever used python or ruby, you may remember writing in this way:
```python
a, b = (1, 2) # a=>1, b=>2
```
So that you can write `swap` like this, think about what a painful when writing `swap` in C:      
```python
b, a = (a, b) # a=>2, b=>1
```

This is Destructuring Patterns, what is the relationship with Pattern Match? The answer is left side of the equation is a Pattern, the Pattern Match matches right side according the pattern in left side. So you can think about `=` is doing the pattern matching here.

[ES6](http://kangax.github.io/compat-table/es6/#Destructuring) has been supporting Destructuring, and has ability to destruct Object.

> **note** All codes should be tested in Firefox.

```js
function hello(name){
    return {
        name: name,
        greet:{
            cn: "你好",
            en: "hello"
        }
    }
}
var {name:a, greet:{cn:b}} = hello('world');
// a => world
// b => 你好
```

###List Compreshensions

Still in python, you may have seen the code that generates a new array doubling each element:

```python
[x*2 for x in range(5)] # => [0,2,4,6,8]
```

Congratulations, you can also write in ES6 like this.

```js
[x*2 for (x of [0,1,2,3,4])]
```

Cool, isn't it. Let's write a piece of codes using previous Pattern Match. Suppose we have an array of `hello` object, we want to pick up the Chinese greeting, and append a name, then compose a new array.

```js
[cn+name for ({name, greet:{cn}} of [hello('Li Lei'), hello('Han MeiMei')])]
// => ['你好Li Lei', '你好Han MeiMei']
```

Pattern Match is very useful, isn't it?

> **comment** In Haskell, the function still can be applied Pattern Match. While in JavaScript, Pattern Match for function is difficult to implement, because JavaScript doesn't have Type System. For instance, in Haskell, the type of `hello` is 
`hello::(String a, HashTable b) -> a -> b`, but in JavaScript, `hello` accepts anything, and doesn't check the type of parameter.

but if you really like the pattern matching thing, you probably can use [livescript](http://livescript.net), or tiny lib [pun](https://github.com/CRogers/pun)

[livescript fibonacci](http://jsbin.com/budiga/1/edit?js)