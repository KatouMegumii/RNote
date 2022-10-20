# 总结统计数据：统计摘要、复合操作和初步图像可视化

部分内容来自70011的Lab 2&3，[R语言笔记-缺失值的处理](https://blog.csdn.net/ethmery/article/details/109152730)，[心惊梦醒的笔记](https://www.jianshu.com/p/66118f431bf6)，以及[R语言中的管道](https://www.jianshu.com/p/c65dbce983dd)

## 数据的分组

`group_by()`

------

## 函数summarise()

```R
summarise(flights, delay = mean(dep_delay, na.rm = TRUE))
```

**未完待续**

------

## 实现复合操作：管道传参函数 `%>%`

来自于包 `dplyr`，可以将前一步的结果直接传递给下一步，从而跳过赋值

假设我们需要计算函数在 $f=sin(x+1)^2$ 在 $x=4$ 处的值，在R中实现该步骤需要通过如下步骤：

```R
f1 <- function(x){return(x+1)}
#计算a = x+1的值
f2 <- function(x){return(x^2)}
#计算b = a^2的值
f3 <- function(x){return(sin(x))}
#计算c = sin(b)的值
```
