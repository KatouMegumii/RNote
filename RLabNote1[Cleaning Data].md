# 数据清洗：缺失值、添加数据和功能函数

部分内容来自70011的Lab 2&3，[R语言笔记-缺失值的处理](https://blog.csdn.net/ethmery/article/details/109152730)，[心惊梦醒的笔记](https://www.jianshu.com/p/66118f431bf6)，以及[R语言中的管道](https://www.jianshu.com/p/c65dbce983dd)

## 关于缺失值：函数和运算

### 两个关于缺失值的函数

`is.na()`可以用于判断对象中的每个元素是否为缺失值，输出的是布尔型向量

```R
> is.na(c(1,NA,3)>2)
[1]FALSE TRUE FALSE
```

`na.rm`即remove NA，用于在函数中声明忽略缺失值，如数据中存在缺失值，部分函数只可对非缺失值进行运算，因此如果不提前将缺失值排除，会在最后的结果中返回 `NA`

### `NA`的运算

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
#按照年降序排序
```

#### 注意

后面参数的排序并不会打乱前面已经排序完成的数据，如果会打乱前面已经完成的排序，这个参数将不生效

```R
> arrange(flights,dep_time,sched_dep_time,dep_delay)
# A tibble: 336,776 x 19
    year month   day dep_time sched_~1 dep_d~2 arr_t~3 sched~4 arr_d~5 carrier flight tailnum origin
   <int> <int> <int>    <int>    <int>   <dbl>   <int>   <int>   <dbl> <chr>    <int> <chr>   <chr> 
 1  2013     4    10        1     1930     271     106    2101     245 UA        1703 N33203  EWR   
 2  2013     5    22        1     1935     266     154    2140     254 EV        4361 N27200  EWR   
 3  2013     6    24        1     1950     251     105    2130     215 AA         363 N546AA  LGA   
 4  2013     7     1        1     2029     212     236    2359     157 B6         915 N653JB  JFK   
 5  2013     1    31        1     2100     181     124    2225     179 WN         530 N550WN  LGA   
 6  2013     2    11        1     2100     181     111    2225     166 WN         530 N231WN  LGA   
 7  2013     3    18        1     2128     153     247    2355     172 B6          97 N760JB  JFK   
 8  2013     6    25        1     2130     151     249      14     155 B6        1371 N607JB  LGA   
 9  2013     2    24        1     2245      76     121    2354      87 B6         608 N216JB  JFK   
10  2013     1    13        1     2249      72     108    2357      71 B6          22 N206JB  JFK
# ... with 336,766 more rows, 6 more variables: dest <chr>, air_time <dbl>, distance <dbl>,
#   hour <dbl>, minute <dbl>, time_hour <dttm>, and abbreviated variable names 1: sched_dep_time,
#   2: dep_delay, 3: arr_time, 4: sched_arr_time, 5: arr_delay
```

#### 问题

可以很明显的从打出的缩略表中看到，`dep_delay`并没有按照升序排列，反而是降序排列且也没有使用`desc()`，这是为何呢？

因为如果按照`dep_delay` 排序，会打乱前面已经排序好的结果，`dep_delay`是前两项的差值，自然是降序排列，而实际上第二个参数的排序是在第一个参数排序完成的基础上在第一个排序第一个值不被打乱的情况下进行后续的操作，比如说对于这个例子中的`dep_time`，值为1的数据有N个，此时他们的第二行`sched_dep_time`是混乱的，此时第二个参数生效，在`dep_time`值为1的数据内部对`sched_dep_time`进行升序排序，为了验证这点可以将代码中第二个参数和第三个参数的位置互换：

```R
> arrange(flights,dep_time,dep_delay,sched_dep_time)
# A tibble: 336,776 x 19
    year month   day dep_time sched_~1 dep_d~2 arr_t~3 sched~4 arr_d~5 carrier flight tailnum origin
   <int> <int> <int>    <int>    <int>   <dbl>   <int>   <int>   <dbl> <chr>    <int> <chr>   <chr> 
 1  2013    11    13        1     2359       2     442     440       2 B6        1503 N627JB  JFK   
 2  2013    12    16        1     2359       2     447     437      10 B6         839 N607JB  JFK   
 3  2013    12    20        1     2359       2     430     440     -10 B6        1503 N608JB  JFK   
 4  2013    12    26        1     2359       2     437     440      -3 B6        1503 N527JB  JFK   
 5  2013    12    30        1     2359       2     441     437       4 B6         839 N508JB  JFK   
 6  2013     4     5        1     2359       2     410     339      31 B6         727 N606JB  JFK   
 7  2013     5    25        1     2359       2     336     341      -5 B6         727 N523JB  JFK   
 8  2013     6    20        1     2359       2     340     350     -10 B6         745 N517JB  JFK   
 9  2013     7    27        1     2359       2     345     340       5 B6         839 N503JB  JFK   
10  2013     7    28        1     2359       2     423     350      33 B6         745 N703JB  JFK   
# ... with 336,766 more rows, 6 more variables: dest <chr>, air_time <dbl>, distance <dbl>,
#   hour <dbl>, minute <dbl>, time_hour <dttm>, and abbreviated variable names 1: sched_dep_time,
#   2: dep_delay, 3: arr_time, 4: sched_arr_time, 5: arr_delay
# i Use `print(n = ...)` to see more rows, and `colnames()` to see all variable names
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

#### 关于`NA`

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

#### 是否存在顺序问题？

`select()`中变量名的顺序不同会影响输出结果的顺序

```R
> select(flights,month,year,day)
> select(flights,year,month,day)
#两者的输出结果的排序方式不同
#这点和直接使用数据框输出是一样的，直接使用数据框输出也具有顺序性
```

#### 多样化的表达

`select()`中的变量可以使用多种方式来表示，同样也包括使用逻辑运算

**注意：** 取反除了使用通用逻辑符号`!`之外，还可以使用符号`-`，两者的效果是完全一样的

### `select()`：进阶用法

#### 筛选满足条件的数据

`starts_with()`,`ends_with()`,`contains()`,`matches()`,`num_range()`，具体参数如下

```R
#ignore.case=TRUE时忽略大小写
#vars不知道具体永雏，但是如果其值是表存在的列且不在选择结果中会额外输出一列，若在结果中会用vars代替原本列的名字
#以某字开始的结果
starts_with(match, ignore.case = TRUE, vars = NULL)
#以某字结束的结果
ends_with(match, ignore.case = TRUE, vars = NULL)
#包含某字的结果
contains(match, ignore.case = TRUE, vars = NULL)
#使用例
select(flights,starts_with("dep"))
select(flights,starts_with(c("dep", "Sarr"))
select(flights,ends_with("time"))
select(flights,contians("time"))

#per1是指是否启用PCRE正则表达式函数库
#这里的参数可以用正则表达式来写，正则表达式的具体内容见正文
matches(match, ignore.case = TRUE, perl = FALSE, vars = NULL)
#width有什么用我也不知道，帮助文档里也没有写
num_range(prefix, range, suffix = "", width = NULL, vars = NULL)
#使用例:可以规定前缀，后缀，然后中间一串变量
select(flights,num_range("dep_",c("time","delay")))
```

#### 批量操作

`everything()`,`last_col()`,`all_of()`,`any_of()`,`one_of()`

```R
#everything()就是选中所有列
select(flights,everything())
#last_col()是最后一列，可以选最后N列
select(flights,last_col())##最后1列
select(flights,last_col(5))##最后5列
```

```R
#可以另写一个向量来用于select()函数
vars=c("dep_time","arr_time")
select(flights,all_of(vars))##选中vars里所有的列
#如果vars里面含有表中没有的元素怎么办？
> vars=c("null","dep_time","arr_time")
> select(flights,all_of(vars))
select(flights,all_of(vars)) 中的错误 (RLabTest.R60): Problem while evaluating `all_of(vars)`.
#会直接报错
#可以使用any_of来解决这个问题
> select(flights,any_of(vars))
# A tibble: 336,776 x 2
   dep_time arr_time
      <int>    <int>
 1      517      830
 2      533      850
 3      542      923
 4      544     1004
 5      554      812
 6      554      740
 7      555      913
 8      557      709
 9      557      838
10      558      753
# ... with 336,766 more rows

#one_of()是一个通用遍历函数，能达到和any_of()类似的效果
> select(flights,one_of(vars))
Warning: Unknown columns: `null` ##对没有的列会报错
#最后还是会输出和上表相同的结果           
```

#### 函数操作

`where()`中间可以使用某种函数，筛选出来的值是函数返回`TRUE`值的数据

```R
#表iris，前四行是numeric，第五行是factor
#函数is.factor(),is.numeric()分别用来判断对应的型
> select(iris,where(is.factor))
# A tibble: 150 x 1
 Species
   <fct>  
1 setosa 
2 setosa 
3 setosa 
4 setosa 
# ... with 146 more rows
> select(iris,where(is.numeric))
# A tibble: 150 x 4
  Sepal.Length Sepal.Width Petal.Length Petal.Width
         <dbl>       <dbl>        <dbl>       <dbl>
1          5.1         3.5          1.4         0.2
2          4.9         3            1.4         0.2
3          4.7         3.2          1.3         0.2
4          4.6         3.1          1.5         0.2
# ... with 146 more rows

```

正则函数表达式的内容从讲义上来看以后会在字符串的内容里了解到，暂时可以参考[维基百科](https://zh.wikipedia.org/wiki/%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F)

~~如果以后有新的内容会开一个新的文档专门写，因为看起来内容蛮多的~~ 已经新开了一个文档[RNoteExtra1](https://github.com/KatouMegumii/RNote/blob/master/RNoteExtra1%5BRegular%20Expression%5D.md)了

`perl`正则函数表达库这玩意疑似很麻烦，但是好像又有点用，可以看一看[具体内容](https://www.cyut.edu.tw/~ckhung/b/re/rules.php)

## 三个函数之间的比较

`,`在`select()`函数中和在`arrange()` `filter()`中表达的逻辑含义是不同的，执行的逻辑和输出的结果也不同，其中有些可以通过逻辑含义相同的逻辑运算符来代替`,`

#### `arrange()`并列参数

优先按照第一个变量排序，后一个变量会改变前一个排序的结果，而逻辑运算符不生效

```R
> arrange(flights,dep_time,sched_dep_time) ##正常排序
> arrange(flights,dep_time&sched_dep_time)
> arrange(flights,dep_time|sched_dep_time) ##按年份排序
#为何会出现以上的情况？
#猜测原因是因为函数内使用的逻辑运算直接将某列按照数字型运算了，输出了TRUE，在数字表达里为1，所以恒按照第一列排序，等于如下代码
> arrange(flights,1)
#通过使用一个非数字型来验证，发现逻辑运算不执行了，因为字符串没办法参与到逻辑运算中
> arrange(flights,dep_time&dest)
arrange(flights,dep_time&dest) 中的错误 (RLabTest.R#47): Problem with the implicit `transmute()` step.
x Problem while computing `..1 = dep_time & dest`.
Caused by error in `dep_time & dest`:
! operations are possible only for numeric, logical or complex types
```

#### `filter()`并列参数

执行的逻辑运算结果是筛选出符合每个条件的数据，相当于`&`，但是逻辑运算符没有办法代替`,`将参数隔开的功能，多个参数通过逻辑运算符并联后视为一个参数

```R
#以下两行代码输出的内容是一致的
> filter(flights,day==1,month==1,year==2013)
> filter(flights,day==1&month==1&year==2013)、

#逻辑运算符不能分割参数，且优先运算并将结果当成一个整体，如想筛选出1月1号和2月2号的所有数据
> filter(flights,day==1,month==1|day==2,month==2)
# A tibble: 0 x 19
# ... with 19 variables: year <int>, month <int>, day <int>, dep_time <int>, sched_dep_time <int>,
#   dep_delay <dbl>, arr_time <int>, sched_arr_time <int>, arr_delay <dbl>, carrier <chr>,
#   flight <int>, tailnum <chr>, origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>,
#   hour <dbl>, minute <dbl>, time_hour <dttm>
# i Use `colnames()` to see all variable names

#因为实际上month==1|day==2变成一个大参数了，这个筛选条件实际上是所有1号的数据和所有2月的数据和所有1月2号的数据取交集，交集显然是个空集
#正确的写法如下
> filter(flights,(day==1&month==1)|(day==2&month==2))
# A tibble: 1,524 x 19
    year month   day dep_time sched_~1 dep_d~2 arr_t~3 sched~4 arr_d~5 carrier flight tailnum origin
   <int> <int> <int>    <int>    <int>   <dbl>   <int>   <int>   <dbl> <chr>    <int> <chr>   <chr> 
 1  2013     1     1      517      515       2     830     819      11 UA        1545 N14228  EWR   
 2  2013     1     1      533      529       4     850     830      20 UA        1714 N24211  LGA   
 3  2013     1     1      542      540       2     923     850      33 AA        1141 N619AA  JFK   
 4  2013     1     1      544      545      -1    1004    1022     -18 B6         725 N804JB  JFK   
 5  2013     1     1      554      600      -6     812     837     -25 DL         461 N668DN  LGA   
 6  2013     1     1      554      558      -4     740     728      12 UA        1696 N39463  EWR   
 7  2013     1     1      555      600      -5     913     854      19 B6         507 N516JB  EWR   
 8  2013     1     1      557      600      -3     709     723     -14 EV        5708 N829AS  LGA   
 9  2013     1     1      557      600      -3     838     846      -8 B6          79 N593JB  JFK   
10  2013     1     1      558      600      -2     753     745       8 AA         301 N3ALAA  LGA   
# ... with 1,514 more rows, 6 more variables: dest <chr>, air_time <dbl>, distance <dbl>,
#   hour <dbl>, minute <dbl>, time_hour <dttm>, and abbreviated variable names 1: sched_dep_time,
#   2: dep_delay, 3: arr_time, 4: sched_arr_time, 5: arr_delay
# i Use `print(n = ...)` to see more rows, and `colnames()` to see all variable names
```

#### `select()`并列参数

执行的结果是筛选出每个参数那一列的数据，相当于`|`

```R
#以下两种写法一样
> select(flights,year,month,day)
> select(flights,year|month|day)

#select()函数相对前两种函数来说没有那么严苛
#和向量类似，甚至还可以用：和c()
#以下写法同上两种写法一样
> select(flights,1:3)
> select(flights,year:day)
> select(flights,c(year,month,day))
> select(flights,c("year","month","day"))

```

