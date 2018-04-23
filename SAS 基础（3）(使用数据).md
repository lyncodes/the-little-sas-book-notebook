# SAS 基础（3）(使用数据)

## 目录

[TOC]



## 创建变量

**创建变量方法和python无异！！**

`variable = expression`,只要右侧的expression是合法的。

示例代码：

```SAS
a = 10;
a = 'what?';
b = a;
c = f(a,b)
*f是数学上的函数符号
*只要表达式是合法的即可
```

**注：**

如果有缺失值作为自变量参与函数计算，那么结果也是缺失值。

## 使用SAS函数

SAS包含几百个函数，主要应用于：

- 字符、数字
- 日期、时间
- 金融
- 数学、概率
- 宏
- ……………………………………

### 函数形式

```SAS
function-name(arg1,arg2,…………)；
```

这一点和C语言、python很相似又不一样，C语言和python用当前语句是在定义函数，如果要使用还要再调用一次，而在SAS中，定义的同时就已经调用了。

常用SAS函数：

#### SAS字符函数

- `ANYALNUM,ANYALPHA,ANYDIGIT,ANYSPACE,CAT,CATS,CATX,COMPRESS,INDEX,LEFT,LENGTH,PROPCASE`
- `SUBSTR,TRANSLATE,TRANWRD,TRIM,UPCASE`

具体用法，请参考[SAS FUNCTION SUPPORT](http://support.sas.com/documentation/cdl/en/lrdict/64316/HTML/default/viewer.htm#a000245852.htm)

#### SAS数值函数

- `INT、LOG、LOG10、MAX、MEAN、MIN、N、NMISS、ROUND、SUM`
- `DATEJUL、DAY、MDY、MONTH、QTR、TODAY、WEEKDAY、YEAR、YRDIF`

具体用法，请参考[SAS FUNCTION SUPPORT](http://support.sas.com/documentation/cdl/en/lrdict/64316/HTML/default/viewer.htm#a000245852.htm)

## SAS逻辑

### 使用IF-THEN语句（类似于其他高级语言）

语法：

```SAS
IF condition THEN action;
```

示例代码:

```SAS
IF Model = "Berlinetta" THEN Make = 'Ferrari';
```

先判断，如果Model是Berlinetta，就把Make赋值为Ferrari。

逻辑符号（和C语言无打的差别，仅限于不等于的写法）：

- `=`等于

- `~=`**不等于**

- `<`小于

- `>`大于

- `>=`大于等于

- `<=`小于等于

**`IN`**运算符：

```SAS
IF Model IN ('Model T','Model A') THEN Make = 'Ford';
```

判断Model变量的值是否在后面这个列表中，也就是是否和其中一个字符串相等！如果匹配成功，则执行后面的赋值语句。

### IF-THEN-DO-END语句

不像C语言中的花括号实现语句块，SAS通过DO-END来实现一次逻辑判断，多次命令执行。

```SAS
IF condition THEN DO;
	action1;
	action2;
END;
```

同时也可以通过`AND`和`OR来实现更复杂的逻辑。`

```SAS
IF condition1 AND condition2 THEN action;
*综合起来的复逻辑判断和多执行
IF condition AND condition2 ………… THEN DO;
	action1；
	action2;
	………………
END;
```

### IF-THEN/ELSE语句

前面的IF-ELSE仅仅满足一次二元逻辑判断，如果要进行多元逻辑判断，则用ELSE效果会更好。、

语法：

```SAS
IF condition THEN action;
	ELSE IF condition THEN action;
	ELSE IF condition THEN action;
ELSE action；
```

好处是显而易见的：

- 不需要用IF-ELSE 来层层嵌套实现复杂逻辑
- 只需要将可能出现的conditions写入到ELSE-IF语句中即可进行判断
- 程序的运算效率更高，只要满足某个条件，其他的语句就自动跳过了！！！
- **注：所有ELSE-IF的condition必须是互斥的！！**
- 最后的ELSE action表示，如果前面的逻辑都不满足，则最后执行ELSE中的最后的action。
  - 且ELSE action语句只能有一个，且只能放在最后。

示例代码：

```SAS
IF Cost = . THEN CostGroup = 'missing';
	ELSE IF Cost < 2000 THEN CostGroup = 'Low';
	ELSE IF Cost < 10000 THEN CostGroup = 'medium';
	Else CostGroup = 'high';
```

- 判断Cost是否为空值，如果是，则赋值为missing
- 如果不是空值，则判断是小于两千还是小于10000，然后分别赋予相应的low和medium标签
- 最后如果都不满足，则判断Cost是high了。


## 提取数据的子集

**语法和SQL非常相似**

示例代码：

```SAS
IF expression;
IF expression THEN DELETE;
```

实际代码：

```SAS
IF Sex = 'f';
*或者
IF Sex = 'f' THEN DELETE;
```

两条语句的作用正好想法，前者对满足条件的数据进行留存，后者则将满足条件的数据进行删除。

示例数据：

```
A Midsummer Night's Dream	 1595 comedy
Comedy of Errors			1590 comedy
Halmet						1600 tragedy
Macbeth						1606 tragedy
Richard III					1594 history
……………………………………………………
```

```SAS
IF Type = 'comedy'
```

就直接筛选出来话剧类型是comedy的话剧了，像极了SQL。

## 使用SAS日期

### 常见日期输入格式表

```SAS
ANYDTDTEw
DATE
DDMMYY
JULIAN
MMDDYY
```

### 常见日期函数表

```SAS
DATEJUL()
DAY()
MDY()
MOUTH()
QTR()
TODAY()
WEEKDAY()
YEAR()
YRDIF()
```

### 常见日期输出格式

```SAS
DATE
EURDFDD
JULIAN
MMDDYY
WEEKDATE
WORDDATE
```

具体的函数解析，参考[SAS support](http://documentation.sas.com/?docsetId=lefunctionsref&docsetTarget=lefunctionsrefwhatsnew94.htm&docsetVersion=9.4&locale=en)

## RETAIN语句

在DATA步中，每次迭代开始时都会讲所有变量设置为缺失值，比较浪费时间，所以通过RETAIN来改变这种方式。

- **RETAIN**语句保留DATA步上一次迭代中的变量。

- **RETAIN语句可以出现在DATA步中的任意位置。**

  ```SAS
  RETAIN variable-list;
  RETAIN variable-list initial-value;
  *甚至为变量指定初始值来代替缺失值
  ```


## 求和语句（适用于累加）

```SAS
variable + expression;
*代码等价于以下

RETAIN variable 0；
variable = SUM(variable,expression);


```

- 此语句将表达式的值累加到变量中
- 将变量的值从DATA步的一个迭代保留到下一个迭代
- 该变量必须是数字，且初始值是0

实力数据：

```
6-19	columbia peaches		8	3
6-20	columbia peaches		10	5
6-23	plains peanuts			3	4
6-24	plains peanuts			7	2
6-25	plains peanuts			12	8
6-30	gilroy garlics			4	4
6-30	gilroy garlics			9	4
7-4		sacramento tomatoes		15	9
7-4		sacramento tomatoes		10	10
7-5		sacramento tomatoes		2	3
```

分别表示比赛的日期、参赛球队、击球次数、跑垒次数。

我们需要在数据集中增加两个变量（本赛季的累计跑垒次数，迄今为止比赛中的最大跑垒次数）

示例代码：

```SAS
……………………
RETAIN MaxRuns；
MaxRuns = MAX(MaxRuns, Runs);
RunsToDate + Runs;
……………………
```

- 用RETAIN语句保留MaxRuns变量，默认初始化为0
- 在将MaxRuns和Runs中的最大值赋值给MaxRuns，完整最大值的更新！！
- 最后，RunsToDate与Runs进行累加计算，计算总的跑垒次数。


## 数组化程序（向量化、矩阵化）

类比与MATLAB，将数据向量化之后，可以极大的提高运算速度（数量级的提升）。

语法：

```SAS
ARRAY name (n) $ variable-list;
```

- name是数组名
- n是数组中的变量个数
- variable-list是变量列表，且列表中的变量名个数，必须等于定义的n


示例代码:

```SAS
ARRAY store (4) Macys Penneys Scars Target;
```

其中，store(1)就是指代Macys，以此类推。

示例数据:

```SAS
albany		54	3	9	4	4	9
richmond	33	2	9	3	3	3
oakland		27	3	9	4	2	3
richmond	41	3	5	4	5	5
berkeley	18	4	4	9	3	2
```

原始数据分别是听众所在城市、年龄、对五首歌的评分，分数应该在1-5之间，其中分数为9的，就是异常值，我们要把9设置为缺失值。

示例代码：

```SAS
INPUT City $ 1-15 Age wj kt tr flip ttr;
ARRAY song (5) wj tr filp ttr;
DO i = 1 TO 5;
	IF song(i) = 9 THEN song(i) = .;
END;
```

五首歌的名字分别是wj kt tr flip ttr，用song（n）来代表五个变量

**`DO i = i TO 5`用来实现类似于C语言中的FOR循环**

## 使用变量名列表的快捷方式

```SAS
INPUT Cat8 - C	Cat12;
*相当于
INPUT Cat8 Cat9 Cat10 Cat11 Cat12;
```

这在定义变量的时候是很方便，但是在后面代码的编写中，可能会让我们对各个变量具体代表的意思不清楚。

> 我本人是不推荐使用的。









