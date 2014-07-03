# 匿名函数(anonymous function)

支持匿名函数, 也就意味着函数可以作为一等公民. 可以被当做参数, 也可以被当做返回值.因此, JavaScript 的支持一等函数的函数式语言, 而且定义一个匿名函数式如此简单.

### 声明一个匿名函数
```js
var a = function(x){
    return x*x;
}
console.log(a)
```
这种定义函数的方式叫一个函数表达式(function expression).
为什么说是表达式, 表达式会产生一个结果,这里表达式产生的结果--匿名函数付给了变量 b. 但是诡异的是这并不等价于创建了一个名叫 b 的函数.
当然表达式还可以构造具名函数
```js
var squareB = function squareA(){
    return x*x;
}
```
