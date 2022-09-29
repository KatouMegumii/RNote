## 2022/09/29 ##

------

#### 关于vector ####
+ 几种声明变量的方式
```R
variable=c(1:10)
variable=c(1,2,3)
variable=c(“a”,”b”)
```
+ `names()`用于给向量附名

` names(vector)=c(a,b,c)`

或者是
```R
name=c(a,b,c)
names(vector)=c(name)
```
+ 向量可以进行运算，如
```R
poker_vector <- c(140, -50, 20, -120, 240)
roulette_vector <- c(-24, -50, 100, -350, 10)
days_vector <- c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday")
names(poker_vector) <- days_vector
names(roulette_vector) <- days_vector
total_daily <- poker_vector+roulette_vector
```
+ 选择向量中的具体值
````R
vector[1] 
vector[1,2]
vector[1:3]
````
+ 不等号可直接用于比较变量并输出布尔值
````R
fruitA_Num=8
fruitB_Num=10
fruitA_Num>fruitB_Num
````

输出：

```R
[1] FALSE
```



- 一个关于筛选向量内符合条件内容并输出的例子

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

- 一些可以用来进行统计学计算的内容

`sum()` 可以用来计算和 ` mean()` 可以用来计算平均数

------

#### 关于Matrix ####

+ 构建一个矩阵

使用` matrix()` 来构建矩阵 ，`byrow`用来表示按列填充，`nrow`用来表示矩阵行数

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

+ 将vector合并成矩阵

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

+ 给矩阵的行和列命名

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

+ 另一种给矩阵行列命名的方式

直接使用`dimnames=list()`进行命名

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

+ 合并矩阵和矩阵求和

`rowSums()`按行求和，同理`colSums()`是按列求和

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

+ 选择矩阵内元素

`my_matrix[r,c]`选择第r行第c列的一个元素

`my_matrix[r1:r2,c1:c2]`选择r<sub>1</sub> - r<sub>2</sub>行，c<sub>1</sub> - c<sub>2</sub>列的N个元素

`my_matrix[,c]`选择第c列元素

`my_matrix[r,]`选择第r行元素

+ 矩阵的运算

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

#### 关于factor ####

+ factor和vector的区别

factor拥有属性`Levels` ，我个人理解为更适合统计学使用的纯粹值，使用`factor()`可将vector转换成factor

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

