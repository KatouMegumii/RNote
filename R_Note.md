# R的基础内容-主要参考datacamp

## 基础函数和表达式

- ### 几种声明变量的方式

```R
#正统的赋值方式 ->
variable -> c(1:10)
#在IDE中可以使用=达到相同的效果
variable=c(1:10)
variable=c(1,2,3)
variable=c("a","b")
```

- ### 基础运算符和函数：如数学运算和描述性变量统计

`sum()` 可以用来计算和 ` mean()` 可以用来计算平均数

`sqrt()`开根号，`exp()`自然常数的N次方，`log(m,n)`m以为n底的对数，`log10(m)`m以10为底的对数

`sin()` `asin()` `cos()` `acos()` `tan()` `atan()` R中三角函数和反三角函数使用的是弧度制而非分度制

取整函数 `round()` `ceiling()` `floor()`：

```R
#直接四舍五入取整仅有一个参数n, 在取整位是偶数的时候5会被舍去
> round(3.5)
[1]4
> round(4.5)
[1]4
#也可以规定另一个参数m, 对n取m位小数的整数
> round(4.256,1)
[1]4.3
> round(4.256,2)
[1]4.26
#对n向上取整和向下取整
> ceiling(4.25)
[1]5
> floor(4.25)
[1]4
```

+ ### 概率分布函数：`d` `p` `q` `r` 四种不同的分布函数

**未完待续**

+ <h3 id="1">关于比较运算：赋值，运算符和输出值</h3>

部分内容参考自[R语言教程](https://www.math.pku.edu.cn/teachers/lidf/docs/Rbook/html/_Rbook/prog-type-logi.html)，R可以通过比较运算符直接输出一个布尔值

````R
fruitA_Num=8
fruitB_Num=10
fruitA_Num>fruitB_Num
````

输出：

```R
[1] FALSE
```

R中的比较运算符只有这几种 `==（等于）` `!=（不等于）` `>` `>=` `<` `<=` ，`=`在R中仅用于赋值，对于浮点数，使用 `==`时需要注意如下：

```R
#因为使用优先精度算法，非整数只能储存为近似值，所以导致了数学含义上正确的表达式输出了不同的结果
> sqrt(2) ^ 2 == 2
[1] FALSE
> sqrt(4) ^ 2 == 4
[1] TRUE
> 1 / 49 * 49 == 1
[1] FALSE

#可以用near()函数解决这个问题
> near(sqrt(2) ^ 2,  2)
[1] TRUE
> near(1 / 49 * 49, 1)
[1] TRUE
> near(1 / 2 * 2, 1)
[1] TRUE

#near()函数的参数信息：x,y分别是两个浮点数或浮点数向量，tol参数是比较时的容错
near(x, y, tol = .Machine$double.eps^0.5)
```

还有一种特殊的运算符 `%in%`，表示属于，其运算结果是一个逻辑型向量

```R
> c(1,3) %in% c(2,3,4)
[1]FALSE TRUE

> c(NA,3) %in% c(NA,2,3,4)
[1]TRUE TRUE
```

函数 `match(x,y)`和 `%in%`有类似的效果，但时其不会返回布尔值，而是对x中的每一个元素返回其首次在y中出现的位置，找不到时返回缺失值

```R
> match(c(1, 3), c(2,3,4,3))
[1] NA  2
```

+ <h3 id="2">关于逻辑运算：运算符和含义</h3>

部分内容参考自[R语言教程](https://www.math.pku.edu.cn/teachers/lidf/docs/Rbook/html/_Rbook/prog-type-logi.html)和[R语言基础运算](https://www.runoob.com/r/r-basic-operators.html)，R可以对对象进行逻辑运算，且逻辑运算符可以组合，具体运算符如下：

`&` 与（and），`A&B`在统计学上表示集合A和集合B的交集

`|` 或（or），`A|B`在统计学上表示集合A和集合B的并集

`!` 非（not），`!A`在统计学上表示集合A的补集

`xor`异或（exclusive or），`xor(A,B)`在统计学上表示集合AB的交集与补集差，即<img src="https://latex.codecogs.com/svg.image?(\overline{A}\cap&space;B)\cup&space;(A\cap&space;\overline{B})" title="https://latex.codecogs.com/svg.image?(\overline{A}\cap B)\cup (A\cap \overline{B})" />

![](https://s3.bmp.ovh/imgs/2022/10/17/1f99f7f9eb3d53c1.png)

`&&`和 `||`：是仅对两个标量进行运算的与和或，如果有向量也仅使用第一个元素

```R
> v=c(3,0,TRUE,2+2i)
  t=c(1,3,TRUE,2+3i)
  print(v&&t)
[1]TRUE
> v=c(0,0,TRUE,2+2i)
  t=c(0,3,TRUE,2+3i
  print(v||t)
[1]FALSE
#大多适用于if和while循环中
> if(TRUE||sqrt(-1)>0) next
#其中sqrt(-1)>0的部分不会执行
```

在右运算符缺失时，如果左运算符能够确定结果真假，可以得到非缺失的结果：

```R
#逻辑运算中0和1有布尔含义，非0的数字与1相同
> 1&0
[1]FALSE
> 11|12
[1]TRUE
#以下是用布尔变量和空值参与逻辑运算的几种情况
> TRUE|NA
[1]TRUE
> FALSE|NA
[1]FALSE
> TRUE&NA
[1]NA
> FASLE&NA
[1]FALSE
```

+ ### 关于逻辑运算函数：类型和返回值

部分内容参考自[R语言教程](https://www.math.pku.edu.cn/teachers/lidf/docs/Rbook/html/_Rbook/prog-type-logi.html)

`all()` `any()` `which`()

`identical(x,y)` `all.equal()`

`duplicated()` `unique()`

**未完待续**

---

## 关于vector：如何创建和处理向量

- ### `names()`用于给向量赋名

` names(vector)=c(a,b,c)`

或者是

```R
name=c(a,b,c)
names(vector)=c(name)
```

+ ### 向量可以进行运算，如

```R
poker_vector <- c(140, -50, 20, -120, 240)
roulette_vector <- c(-24, -50, 100, -350, 10)
days_vector <- c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday")
names(poker_vector) <- days_vector
names(roulette_vector) <- days_vector
total_daily <- poker_vector+roulette_vector
```

+ ### 选择向量中的具体值

````R
vector[1] 
vector[1,2]
vector[1:3]
````

- ### 一个关于筛选向量内符合条件内容并输出的例子

```R
# Poker and roulette winnings from Monday to Friday:
poker_vector <- c(140, -50, 20, -120, 240)
roulette_vector <- c(-24, -50, 100, -350, 10)
days_vector <- c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday")
names(poker_vector) <- days_vector
names(roulette_vector) <- days_vector

# Which days did you make money on roulette?
selection_vector <- roulette_vector>0

# Select from roulette_vector these days
roulette_winning_days <- roulette_vector[selection_vector]
roulette_winning_days
```

输出：

```R
roulette_winning_days
Wednesday    Friday 
      100        10
```

---

## 关于matrix：矩阵的创建、合并和运算

+ ### 构建一个矩阵

使用 `matrix()` 来构建矩阵 ，`byrow`用来表示按列填充，`nrow`用来表示矩阵行数

```R
matrix(1:9,byrow=TRUE,nrow=3)
```

输出：

```R
matrix(1:9,byrow=TRUE,nrow=3)
     [,1] [,2] [,3]
[1,]    1    2    3
[2,]    4    5    6
[3,]    7    8    9
```

+ ### 将vector合并成矩阵

```R
# Box office Star Wars (in millions!)
new_hope <- c(460.998, 314.4)
empire_strikes <- c(290.475, 247.900)
return_jedi <- c(309.306, 165.8)

# Create box_office
box_office <- c(new_hope,empire_strikes,return_jedi)

# Construct star_wars_matrix
star_wars_matrix <- matrix(box_office,byrow=TRUE,nrow=3)
star_wars_matrix
```

输出：

```R
star_wars_matrix
        [,1]  [,2]
[1,] 460.998 314.4
[2,] 290.475 247.9
[3,] 309.306 165.8
```

+ ### 给矩阵的行和列命名

`rownames()` `colnames()`

```R
# Box office Star Wars (in millions!)
new_hope <- c(460.998, 314.4)
empire_strikes <- c(290.475, 247.900)
return_jedi <- c(309.306, 165.8)

# Construct matrix
star_wars_matrix <- matrix(c(new_hope, empire_strikes, return_jedi), nrow = 3, byrow = TRUE)

# Vectors region and titles, used for naming
region <- c("US", "non-US")
titles <- c("A New Hope", "The Empire Strikes Back", "Return of the Jedi")

# Name the columns with region
colnames(star_wars_matrix)<-region

# Name the rows with titles
rownames(star_wars_matrix)<-titles

# Print out star_wars_matrix
star_wars_matrix
```

输出：

```R
star_wars_matrix
                             US non-US
A New Hope              460.998  314.4
The Empire Strikes Back 290.475  247.9
Return of the Jedi      309.306  165.8
```

+ ### 另一种给矩阵行列命名的方式

直接使用 `dimnames=list()`进行命名

```R
box_office <- c(460.998, 314.4, 290.475, 247.900, 309.306, 165.8)
region <- c("US", "non-US")
titles <- c("A New Hope","The Empire Strikes Back","Return of the Jedi")
   
star_wars_matrix <- matrix(box_office, nrow = 3, byrow = TRUE, dimnames = list(titles, region))

star_wars_matrix
```

输出：

```R
US non-US
A New Hope              460.998  314.4
The Empire Strikes Back 290.475  247.9
Return of the Jedi      309.306  165.8
```

+ ### 合并矩阵和矩阵求和

`rowSums()`按行求和，同理 `colSums()`是按列求和

`rbind()`给矩阵添加额外的行，`cbind()`给矩阵添加额外的列

```R
# star_wars_matrix are available above
star_wars_matrix

# The worldwide box office figures
worldwide_vector <- rowSums(star_wars_matrix)
worldwide_vector

# Bind the new variable worldwide_vector as a column to star_wars_matrix
all_wars_matrix <- cbind(star_wars_matrix,worldwide_vector)
all_wars_matrix
```

输出：

```R
worldwide_vector
A New Hope    The Empire Strikes Back    Return of the Jedi 
   775.398                    538.375               475.106 
  
  
all_wars_matrix
                             US non-US worldwide_vector
A New Hope              460.998  314.4          775.398
The Empire Strikes Back 290.475  247.9          538.375
Return of the Jedi      309.306  165.8          475.106
```

+ ### 选择矩阵内元素

`my_matrix[r,c]`选择第r行第c列的一个元素

`my_matrix[r1:r2,c1:c2]`选择r<sub>1</sub> - r<sub>2</sub>行，c<sub>1</sub> - c<sub>2</sub>列的N个元素

`my_matrix[,c]`选择第c列元素

`my_matrix[r,]`选择第r行元素

+ ### 矩阵的运算

`mean()`依然适用，同时矩阵可以和自然数和矩阵之间发生运算

```R
# all_wars_matrix and ticket_prices_matrix available in your workspace
all_wars_matrix
ticket_prices_matrix

visitors <- all_wars_matrix/5
visitors

visitors_1 <- all_wars_matrix/ticket_prices_matrix
vistors_1
us_visitors <- visitors_1[,1]
mean(us_visitors)
```

输出：

```R
visitors
                           US non-US
A New Hope              92.20  62.88
The Empire Strikes Back 58.10  49.58
Return of the Jedi      61.86  33.16
The Phantom Menace      94.90 110.50
Attack of the Clones    62.14  67.74
Revenge of the Sith     76.06  93.70

visitors_1
                               US    non-US
A New Hope               92.20000  62.88000
The Empire Strikes Back  48.41667  41.31667
Return of the Jedi       44.18571  23.68571
The Phantom Menace      118.62500 138.12500
Attack of the Clones     69.04444  75.26667
Revenge of the Sith      77.61224  95.61224

mean(us_visitors)
[1] 75.01401
```

**注意：**对于数学运算符号，如加减乘除，需要保证两组矩阵的大小完全一致，因为在R内，这种运算代表每一个矩阵位置上的元素进行相应的运算，如果想使用矩阵内积，即完成![](https://latex.codecogs.com/svg.image?\inline&space;\bg{white}N\times&space;P)和![](http://latex.codecogs.com/svg.latex?P*M)矩阵的乘积并最后输出![](http://latex.codecogs.com/svg.latex?N*M)矩阵的话，需要使用运算符 `%*%`

```R
#如正确计算离散分布的平均数
> x=matrix(1:4,byrow=TRUE,nrow=1)
  p=matrix(c(0.25,0.125,0.375,0.25),byrow=FALSE,nrow = 4,ncol = 1)
  E_X=x%*%p
  print(E_X)
      [,1]
[1,] 2.625

#如直接使用运算符*
> x=matrix(1:4,byrow=TRUE,nrow=1)
  p=matrix(c(0.25,0.125,0.375,0.25),byrow=FALSE,nrow = 4,ncol = 1)
  E_X=x*p
  print(E_X)
[Warning]x * p 中的错误 (Statistics Assessment.R): non-conformable arrays
#non-comformable arrays即两个矩阵大小不同
```

---

## 关于factor：未完待续

+ ### factor和vector的区别

factor拥有属性 `Levels` ，我个人理解为更适合统计学使用的纯粹值，使用 `factor()`可将vector转换成factor

```R
# Sex vector
sex_vector <- c("Male", "Female", "Female", "Male", "Male")
sex_vector

# Convert sex_vector to a factor
factor_sex_vector <-factor(sex_vector)

# Print out factor_sex_vector
factor_sex_vector
```

输出：

```R
sex_vector
[1] "Male"   "Female" "Female" "Male"   "Male"  

factor_sex_vector
[1] Male   Female Female Male   Male  
Levels: Female Male
```

# R数据处理实操-主要来自SOST 70011

部分内容来自70011的Lab 2&3，[R语言笔记-缺失值的处理](https://blog.csdn.net/ethmery/article/details/109152730)，[心惊梦醒的笔记](https://www.jianshu.com/p/66118f431bf6)，以及[R语言中的管道](https://www.jianshu.com/p/c65dbce983dd)

## 数据清洗：缺失值、添加数据和功能函数 `filter()` `arrange()` `select()`

+ <h3 id="3">关于比较运算：赋值，运算符和输出值</h3>

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

+ ### 筛选函数 `filter()`

第一个参数为需要筛选的数据框，第二个以后的参数是需要筛选的条件，返回值是一个数据框

```R
#对数据框flights筛选出1月1日的航班数据
filter(flights,month==1,day==1)
```

第二个及以后的参数需要是一个表达式，关于表达式的内容可以参照[前文](#1)

同时该函数还可以使用逻辑运算，关于逻辑运算的内容依然参照[前文](#2)，相同的筛选条件通过不同的逻辑运算得到的结果最后是一致的

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

`is.na()`[前文](#3)已经有有提到，可以筛出不完整的数据

```R
> between(1:12,7,9)
[1]FALSE FALSE FALSE FALSE FALSE FALSE  TRUE  TRUE  TRUE FALSE FALSE FALSE

#应用函数的几个例子
#需要筛选出1月到10月的航班数据，可以使用多种方法，between也可以实现相同的效果
filter(flights, between(month,1,10))
#筛选出丢失出发时间的数据，使用is.na可以轻松实现
filter(flights, is.na(dep_time))
```

+ 函数 `arrange()`

---

## 总结统计数据：数据分组总结、实现复合操作和初步图像可视化

+ ### 数据的分组

`group_by()`

```R
summarise(flights, delay = mean(dep_delay, na.rm = TRUE))
```

**未完待续**

+ ### 实现复合操作：管道传参函数 `%>%`

来自于包 `dplyr`，可以将前一步的结果直接传递给下一步，从而跳过赋值

假设我们需要计算函数在![](http://latex.codecogs.com/svg.latex?f(x)=sin(x+1)^2)在![](http://latex.codecogs.com/svg.latex?x=4)处的值，在R中实现该步骤需要通过如下步骤：

```R
f1 <- function(x){return(x+1)}
#计算a = x+1的值
f2 <- function(x){return(x^2)}
#计算b = a^2的值
f3 <- function(x){return(sin(x))}
#计算c = sin(b)的值
```

---

## 假设检验：
test