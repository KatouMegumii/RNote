# 数据清洗：缺失值、添加数据和功能函数

部分内容来自70011的Lab 2&3，[R语言笔记-缺失值的处理](https://blog.csdn.net/ethmery/article/details/109152730)，[心惊梦醒的笔记](https://www.jianshu.com/p/66118f431bf6)，以及[R语言中的管道](https://www.jianshu.com/p/c65dbce983dd)

## 关于缺失值：函数和运算

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

---

## 筛选函数 `filter()`

### `filter()`的参数

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

### 筛选时的注意事项

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

`is.na()`前文已经有有提到，可以筛出不完整的数据

```R
> between(1:12,7,9)
[1]FALSE FALSE FALSE FALSE FALSE FALSE  TRUE  TRUE  TRUE FALSE FALSE FALSE

#应用函数的几个例子
#需要筛选出1月到10月的航班数据，可以使用多种方法，between也可以实现相同的效果
filter(flights, between(month,1,10))
#筛选出丢失出发时间的数据，使用is.na可以轻松实现
filter(flights, is.na(dep_time))
```

---

## 函数 `arrange()`和`select()`

### `arrange()`：参数和用法详解

函数arrange通过列值对行进行排列，且默认按照升序排列

`arrange(.data,...,.by_group=FALSE)`

其中第一个参数是数据集，必须是R支持的数据框架下的数据集（如使用`data.frame()`或者`tibble()`所得到的数据集）

第二个参数是数据框中的变量名，可以支持多个变量名同时排序，但是排序的结果永远是前序参数排列完成后再按照后续参数排列

```R
arrange(flights,year,month,day)
#先按年排序，后按月、日排序
arrange(flights,desc(year))
#按照年降序脾虚
```

第三个参数`by_group`，如果为TRUE，则会首先对已经分组的变量进行排序，这个参数只会对分组过的数据集生效

```R
> by_group=group_by(flights,dest)
#创建一个分组数据集
> test1=arrange(by_group,year,month,day,.by_group = TRUE)
#会优先按照dest列的顺序排序
> test2=arrange(by_group,year,month,day,.by_group = FALSE)
#正常按照年月日的顺序排序
```

### `arrange()`：注意事项

无论是升序还是降序排列，`NA`都会被排在数据的最后

**那么如果想让`NA`在数据最开始显示怎么办呢？**

```R
> data=data.frame(x=c(1,2,NA))
#构造一个带有NA的数据集
> arrange(data,desc(is.na(x)))
   x
1 NA
2  1
3  2
#通过is.na()将NA转化成了TRUE和FALSE进行排序
```

### `select()`：参数和基本用法

数据框本身和向量和矩阵类似，可以使用`[]`来选取数据框内的数据，`select()`的基本功能和这种方法类似，但是存在一定区别

```R
#通过数据框本身筛选出年和月的数据
> flights[c("year","month")]
# A tibble: 336,776 × 2
   month  year
   <int> <int>
 1     1  2013
 2     1  2013
 3     1  2013
 4     1  2013
 5     1  2013
 6     1  2013
 7     1  2013
 8     1  2013
 9     1  2013
10     1  2013
# … with 336,766 more rows
```

最大的区别是在所选的变量重复时，`selecet()`只会输出一列，而使用数据框输出则会输出重复的两列

```R
> select(flights,month,month)
> select(fligjts,month)
#这两者输出的结果是相同的，仅有month一列
> flights(c("month","month"))
#会输出相同的两列数据
```

### `select()`：注意事项

`select()`中变量名的顺序不同会影响输出结果的顺序

```R
> select(flights,month,year,day)
> select(flights,year,month,day)
#两者的输出结果的排序方式不同
#这点和直接使用数据框输出是一样的，直接使用数据框输出也具有顺序性
```

`select()`中的变量可以使用多种方式来表示，同样也包括使用逻辑运算

**注意：** 取反除了使用通用逻辑符号`!`之外，还可以使用符号`-`，两者的效果是完全一样的

## 三个函数之间的比较

`,`在`select()`函数中和在`arrange()` `filter()`中表达的逻辑含义是不同的，执行的逻辑和输出的结果也不同

在`arrange()`中多个变量通过`,`并列，会优先按照第一个变量排序，后一个变量会改变前一个排序的结果

```R

```

