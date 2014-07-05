# map reduce filter

简单的讲,Map 是对一组元素中的每个元素应用某个操作,使之产生相应的变化;Reduce 则是把元素进行"累加", 即让元素相互作用,产生所需的结果.

现在我们来看一个问题,求1-5的平方和.使用命令式的方法,你的解决方法可能如下所示:
```
var square_sum = 0;
for(var i=0;i<=5;i++){
    square_sum += i*i
}
console.log(square_sum) // => 55
```
那么如果使用函数式的 map reduce 呢?
```
[1,2,3,4,5].map(x => x*x).reduce((x,y) => x+y) // => 55
```
优点

1. 无副作用 square_sum
2. 可读性好
3. 函数可组合
example 3
1
3 -5 -7 -2
-3-5-7+2
-1





