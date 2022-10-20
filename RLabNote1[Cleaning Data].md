# 数据清洗：缺失值、添加数据和功能函数 

部分内容来自70011的Lab 2&3，[R语言笔记-缺失值的处理](https://blog.csdn.net/ethmery/article/details/109152730)，[心惊梦醒的笔记](https://www.jianshu.com/p/66118f431bf6)，以及[R语言中的管道](https://www.jianshu.com/p/c65dbce983dd)

<h2 id="1">关于缺失值：函数和运算</h2>

`is.na()`可以用于判断对象中的每个元素是否为缺失值，输出的是布尔型向量

```R
> is.na(c(1,NA,3)>2)
[1]FALSE TRUE FALSE
```

`na.rm`即remove NA，用于在函数中声明忽略缺失值，如数据中存在缺失值，部分函数只可对非缺失值进行运算，因此如果不提前将缺失值排除，会在最后的结果中返回 `NA`

**`NA`和所有的变量进行运算都会返回 `NA`吗？**

```R
#任何数的0次幂都是1
> NA^0
[1]1
#任何数和TRUE的或都是TRUE
> NA | TRUE
[1]TRUE
#但是任何数与0的积都是0在R中并不成立
> NA*0
[1]NA
```

------

## 筛选函数 `filter()`

第一个参数为需要筛选的数据框，第二个以后的参数是需要筛选的条件，返回值是一个数据框

```R
#对数据框flights筛选出1月1日的航班数据
filter(flights,month==1,day==1)
```

第二个及以后的参数需要是一个表达式，关于表达式的内容可以参照[RNote1](https://github.com/KatouMegumii/RNote/blob/master/RNote1%5BBasic%20of%20R%5D.md)

同时该函数还可以使用逻辑运算，关于逻辑运算的内容依然参照[RNote1](https://github.com/KatouMegumii/RNote/blob/master/RNote1%5BBasic%20of%20R%5D.md)，相同的筛选条件通过不同的逻辑运算得到的结果最后是一致的

```R
#如通过不同的表达筛选出延迟时间在2小时以内的航班
#直接筛选delay<=120的数据
filter(flights, arr_delay <= 120, dep_delay <= 120)
#筛选delay>120的数据取非
filter(flights, !(arr_delay > 120 | dep_delay > 120))
```

在书写筛选条件的表达式的时候需要特别注意：

```R
#对于需要筛选11月和12月的航班，以下的表达是否等效？

filter(flights, month == 11 | month == 12)
#使用逻辑运算，可以成功筛选
filter(flights, month %in% c(11, 12))
#使用比较运算符，可以成功筛选
filter(flights,month==11,month==12)
#先执行条件month==11，后执行条件month==12，筛选结果为0，筛选失败
filter(flights,month==(11|12))
#(11|12)的结果执行为TRUE，在数字上下文中为1，所以最后执行条件month==1，筛选出1月的所有航班，筛选失败
```

特别的，对于某些筛选条件可以使用部分函数，如：

`between(x,left,right)`，其中x是一个变量，该函数用于判断x中的元素是否在函数所规定的左界和右界中，最后的返回值是一个布尔型向量

`is.na()`[前文](#1)已经有有提到，可以筛出不完整的数据

```R
> between(1:12,7,9)
[1]FALSE FALSE FALSE FALSE FALSE FALSE  TRUE  TRUE  TRUE FALSE FALSE FALSE

#应用函数的几个例子
#需要筛选出1月到10月的航班数据，可以使用多种方法，between也可以实现相同的效果
filter(flights, between(month,1,10))
#筛选出丢失出发时间的数据，使用is.na可以轻松实现
filter(flights, is.na(dep_time))
```

------

## 函数 `arrange()`

