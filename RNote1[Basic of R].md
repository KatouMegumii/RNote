# R的基础内容：基础函数和表达式

## 几种声明变量的方式

```R
#正统的赋值方式 ->
variable -> c(1:10)
#在IDE中可以使用=达到相同的效果
variable=c(1:10)
variable=c(1,2,3)
variable=c("a","b")
```

---

## 基础运算符和函数：如数学运算和描述性变量统计

`+` `-` `*` `/` `^`基础的数学四则运算和幂运算

`%/%`模运算取整 `%%` 模运算取余

`sum()` 可以用来计算和 ` mean()` 可以用来计算平均数

`sqrt()` $\sqrt{x}$，`exp()` $e^x$ `log()`自然对数 $ln(x)$ `log(m,n)` ${log_{n}}^{m}$ `log2(m)` ${log_{2}}^{m}$  `log10(m)` ${log_{10}}^{m}$

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

---

## 概率分布函数：`d` `p` `q` `r` 四种不同的分布函数

**未完待续**

---

## 关于比较运算：赋值，运算符和输出值

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

---

## 关于逻辑运算：运算符和含义

部分内容参考自[R语言教程](https://www.math.pku.edu.cn/teachers/lidf/docs/Rbook/html/_Rbook/prog-type-logi.html)和[R语言基础运算](https://www.runoob.com/r/r-basic-operators.html)，R可以对对象进行逻辑运算，且逻辑运算符可以组合，具体运算符如下：

`&` 与（and），`A&B`在统计学上表示集合A和集合B的交集

`|` 或（or），`A|B`在统计学上表示集合A和集合B的并集

`!` 非（not），`!A`在统计学上表示集合A的补集

`xor`异或（exclusive or），`xor(A,B)`在统计学上表示集合AB的交集与补集差，即  $(\overline{A}\cap B)\cup (A\cap \overline{B})$

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

---

## 关于逻辑运算函数：类型和返回值

部分内容参考自[R语言教程](https://www.math.pku.edu.cn/teachers/lidf/docs/Rbook/html/_Rbook/prog-type-logi.html)

`all()` `any()` `which`()

`identical(x,y)` `all.equal()`

`duplicated()` `unique()`

**未完待续**
