# 模式匹配(Pattern Matching)

### destructuring
如果你用过 python 或 ruby 什么的， 可能会记得可以这样写
```python
a, b = (1, 2) # a=>1, b=>2
```
所以 swap(还记得 c 写 swap 是怎么写的吗) 就可以写成
```python
b, a = (a, b) # a=>2, b=>1
```

这就是 destructuring patterns, 这跟模式匹配有毛关系啊. 这里等号左边就是pattern (模式), 模式匹配就是吧右边的玩意按左边的模式匹配过来.

目前[ES6](http://kangax.github.io/compat-table/es6/#Destructuring) 已经支持了 destructuring, 记得还是用firefox

```js
function someObject(){
    return {
        name: "world",
        greet:{
            cn: "你好",
            en: "hello"
        }
    }
}
var {name:a, greet:{cn:b}} = someObject();
// a => world
// b => 你好
```

###list compreshensions
还是 python, 你可能会见过生成一个原数组两倍的数组.
```python
[x*2 for x in range(5)] # => [0,2,4,6,8]
```

恭喜你, 同样的在 es6中你也可以这么写了
```js
[for (x of [0,1,2,3,4]) x*2]
```

让我们配合下前面的模式匹配来干这么个事情, 把前面
> **todos** TODO
