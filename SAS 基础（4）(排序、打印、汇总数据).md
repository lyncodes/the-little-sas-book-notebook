# SAS 基础（4）(排序、打印、汇总数据)

## 目录

[TOC]

## 使用SAS PROC

前面说到了，SAS主要包括了DATA步和PROC步，DATA步用于数据的输入，修改，PROC步用于对数据的分析。

```sas
PROC PRINT;
PROC CONTENTS DATA = '-----PATH-----' 
```

- DATA未指定的情况下，将会使用最新创建的数据集！！

### BY语句

BY语句用于将数据集中，具有具有某种同样标签的众多数据进行**分组**！

```sas
BY State;
BY Sex;
```

### TITLE语句和FOOTNOTE语句

```sas
TITLE '这里放入你的标题'；
FOOTNOTE '这里放入你的脚注'
*最多我们可以指定10个TITLE和FOOTNOTE
TITLE2 '这里是新的2号脚注'
*但是TITLE2会替换掉TITLE3的存在
*只使用TITLE会取消掉所有的标题
TITLE;
```

### LABEL语句

```sas
LABEL ReceiveDate  = 'Date order was received'
	ShipDate = 'Date merchandise was shipped'
```

**用LABEL语句，可以对标签进行说明。**

## WHERE语句用于生成子集

where语句用于在数据集中生成子集，在where后添加condition（筛选条件）。这一点和SQL非常相似，据传SQL是借鉴了SAS的语法。

```sas
WHERE condition;
```

condition的常用运算符：

```sas
=					等于
~=					不等于
>					大于
<					小于
>=					大于等于
<=					小于等于
&					AND，表示并集
|					OR，表示或者
IS NOT MISSING		
BETWEEN AND
CONTAINS
IN(LIST)
```

## PROC SORT语句对数据排序

语法：

```sas
PROC SORT;
BY variable-list;
```

- variable-list只有一个变量的时候,如 BY Name；就会按照Name进行分组
- 当variable-list有多个变量的时候，如BY Name Sex，就会在Name分组的基础上，再对Sex进行分组
- 以此类推

```sas
PROC SORT;
BY State DESCENDING City;
```

- SAS中默认按照升序进行排序，若要按照降序，则使用DESCENDING关键字
- **上述代码表示，先按State分组，并且按照A-Z的升序排列，在City中，按照Z-A的降序排列**

## 更改字符数据的排序顺序

第字符的排序要考虑到：

- a-z和A-Z，大小写混合在一起的情况
- 在windows和Z/OS下的排序

1. ASCII排序顺序： 空格、数字、大写字母、小写字母
2. EBCDIC排序顺序：空格、小写字母、大写字母、数字

```sas
PROC SORT SORTSEQ = EBCDIC/ASCII;
```

用于指定相应的编码方式。

### 忽略字母大小写来排序

```sas
PROC SORT SORTSEQ = LINGUISTIC (STRENGTH = PRIMARY);
```

这告诉SAS忽略掉字符串的大小写进行排序。

### 按照数字的大小来进行排序

```sas
PROC SORT SORTSEQ = LINGUISTIC (NUMERIC_COLLATION = ON);
```

这样排序会告诉SAS，把数字等同于数值进行处理：

（系统默认只按照第一个数字进行排序，所有会变成100、1500、200、50）

语义排序后：

```sas
50m freestyle
100m backstroke
200m breaststroke
1500m freestyle
```

## PROC PRINT用于打印数据

### NOOBS

将不会打印数据集的观测号

### LABEL

如果前面用LABEL语句定义了变量的说明，将会打印出相关的说明。

```sas
PROC PRINT DATA = data-set NOOBS LABEL;
```

示例数据集：

```
Adriana		21	3/21/2012	MP	7
Nathan		14	3/21/2012	CD	19
Matthew		14	3/21/2012	CD	14
Claire		14	3/22/2012	CD	11
Ian			21	3/24/2012	MP	18
…………………………
```

**变量分别是学生姓名、班级号、交钱的日期、糖果类型、售出的数量。**

示例代码：

```sas
PROC SORT DATA = sales；
	BY Class;
PROC PRINT DATA = sales;
	BY Class;
	SUM Profit;
	VAR Name DateReturned CandyType Profit;
```

BY Class指定按班级进行打印，一共打印两张表，一张是14班，一张是21班。

SUM Profit计算出总的利润

VAR指定需要打印的所有变量。

## 调整打印输出的格式

我们用`FORMAT`命令调整输出的格式。

```sas
FORMAT Profit DOLLAR8.2 Loss DOLLAR8.2 SaleDate MMDDYY8.;
```

分别将利润、损失、销售日期和dollar符号和月日年格式联系起来。

`w.d`:

w代表width，既是输出宽度

d代表decimal，既是输出精度（小数点后精度）

> 这一点和C语言非常像！

注：

- FORMAT在DATA步中使用，则和数据集一起存储，永久关联输出格式
- FORMAT在PROC步中使用，则数据格式的关联是临时的，仅仅影响输出过程的结果。

## 可供选择的标准输出格式：



SAS提供相当丰富的输出格式，详情请参考[SAS FORMAT CATEGORIES](http://documentation.sas.com/?docsetId=ds2ref&docsetTarget=n1hmsy77coc9mjn1pk9likyqhuga.htm&docsetVersion=9.4&locale=en)

## 使用PROC FORMAT创建自己的输出格式

语法格式：

```sas
PROC FORMAT;
	VALUE name range-1 = 'formatted-text-1'
				range-2 = 'formatted-text-2'
						.
						.
						.
				range-n = 'formatted-text-n'
```

其中：

- name是我们自定义的输出格式的名称
- 如果是字符型数据，则必须以美元符号开头。即是`$name`
- name的命名规则服从变量名的命名规则

示例数据：

```
19			1	14000	Y
45			1	65000	G
72			2	35000	B
31			1	44000	Y
58			2	83000	W
```

变量分别是客户的年龄、客户的性别（1代表男，2代表女）、年收入、个人颜色喜好（Y黄色，G灰色，B蓝色，W白色）。

示例代码：

```sas
.
.
.
PROC FORMAT;
	VALUE gender 1 = 'Male'
				 2 = 'Female';
	VALUE agegroup 13 -< 20 = 'Teen'
				   20 -< 65 = 'Adult'
				   65 -< HIGH = 'Senior';
	VALUE $col 'W' = 'Moon White'
			   'B' = 'Sky Blue'
			   'Y' = 'Sunburst Yellow'
			   'G' = 'Rain Cloud Gray';
```

```sas
PROC PRINT DATA = carsurvey;
	FORMAT Sex gender. Age agegroup. Color $col. Income DOLLAR8.;
```

**在PRINT**语句中，Sex在gender前面，Age在agegroup前，Color在$col前，且最后要跟一个点号码。

> **则在输出的时候，gender变量中的1就会被替换成为Male，2就会被替换成为Female，其余的以此类推。**

## 使用PROC MEANS汇总数据

```sas
PROC MEANS options;
```

用于对数据集中非缺失值的个数、均值、标准差、最大值、最小值进行统计。

options有很多选项：

- MAXDEC = n，指定显示得小数位数
- MISSING,将缺失值视为有效的汇总组
- 其余的MAX,MIN,MEAN,MEDIAN,MODE等等不再赘述

## 将汇总统计量写入SAS数据集

例子：

仪器记录的是每分钟的数据量，但是我们需要的是每小时的平均数据，那就通过MEAN命令来计算每小时的平均温度来精简数据。

语法：

```sas
OUTPUT OUT = data-set output-statistic-list;
```

data-set是包含结果的SAS数据集

output-statistic-list定义我们所需的统计量和关联的变量名

示例代码：

```sas
PROC MEANS DATA = zoo NOPRINT;
	VAR Lions Tigers Bears;
OUTPUT OUT = zoosum MEAN(Lions Bears) = LionWeight BearWeight;
RUN;
```

解释：

- zoosum是要新生成的SAS数据集，该数据集包含LionWeight和BearWeight两个变量。
- NOPRINT告诉SAS无须生成打印结果

示例数据：

是一个苗圃批发的数据，包含了客户ID、销售日期、销售的牵牛花、金鱼草、金盏花的数目。

```
756-01	05/04/2013	120	80	110
834-01	05/12/2013	90	160	60
901-02	05/18/2013	50	100	75
834-01	06/01/2013	80	60	100
756-01	06/11/2013	100	160	75
901-02	06/19/2013	60	60	60
756-01	06/25/2013	85	110	100
```

示例代码：

```sas
*按照变量Customer ID计算均值，把总和及均值输出到新的数据集
PROC MEANS NOPRINT DATA = sales；
	BY CustID;
	VAR Petunia SnapDragon Marigold;
	OUTPUT OUT = totals
		MEAN(Petunia SnapDragon Marigold) = MeanP MeanSD MeanM
		SUM(Petunia SnapDragon Marigold) = Petunia SnapDragon Marigold;
```

- MEAN和SUM函数，用于计算三种花的均值和总和。

## 使用`PROC FREQ`为数据计数

语法：

```sas
PROC FREQ;
	TABLES variable-combinations;
```

```SAS
PROC FREQ;
	TABLES Sex * YearsEducation;
*生成交叉表
```

## 使用PROC TABULATE生成数据报表

TABULATE命令在SAS是生成报表的专用工具，可以生成非常精美的报表，而且支持我们的定制。

基本语法：

```SAS
PROC TABULATE;
	CLASS classification-variable-list;
	TABLE page-dimension, row-dimension, column-dimension;
```

- class指定具体的变量用于将观测进行分组。
- table语句用于组织表的形式和计算相关的数字。
- 每个table语句只定义一个表格，可以用多个table语句定义多个表格

> 默认情况下，class命令 会排除掉具有缺失值的观测，如果要保留缺失值，则需要添加MISSING选项

```SAS
PROC TABULATE MISSING;
```

示例数据：

```sas
silent lady		maalea		sail		sch		95.00		64
america II		maalea		sail		yac		72.95		65
aloha anai		lahaina		sail		cat		112.00		60
ocean spirit	maalea		power		cat		62.00		65
.
.
.
```

分别表示：

船的名字、船籍港口、帆船还是机动船、船类型（纵帆船、双体船、游艇）、出行价格、船体长度

示例代码：

```sas
PROC TABULATE DATA = boats;
	CLASS Port Locomotion Type;
	TABLE Port, Locomotion, Type;
```

- port是对应的港口，又maalea和lahaina两个港口。
- locomotion是船是sail还是power
- Type是船的类型，是yac、cat、sch

> port对应page-dimension
>
> locomotion对应row-dimension
>
> power对应column-dimension

所以生成的报表将会：

- 包含两页，一页是maalea，另一页是lahaina
- 每一页包含两行，分别是sail和power
- 每一页包含散列，分别是cat、sch、yac

## 将统计量添加到PROC TABULATE输出中

默认情况下，TABULATE会生成简单的计数，但是我们可以在维度内进行连接和叉乘，输出复杂的函数关系。

```SAS
PROC TABULATE DATA = boats;
***********************
	VAR analusis-variable-list;
***********************
	CLASS Port Locomotion Type;
	TABLE Port, Locomotion, Type;
```

VAR命令用于告诉SAS哪些变量包含连续数据。

还是拿上一个例子：

原始数据：

```SAS
silent lady		maalea		sail		sch		95.00		64
america II		maalea		sail		yac		72.95		65
aloha anai		lahaina		sail		cat		112.00		60
ocean spirit	maalea		power		cat		62.00		65
.
.
.
```

船的名字、船籍港口、帆船还是机动船、船类型（纵帆船、双体船、游艇）、出行价格、船体长度

示例代码：

```SAS
PROC TABULATE DATA = boats;
	CLASS Locomotion Type;
	VAR Price;
	TABLE Locomotion ALL,MEAN*Price*(Type ALL);
```

- ALL表示添加一列、或者一列来显示合计

### 连接、叉乘、分组

在维度内，可以连接、叉乘、分组变量和关键字

- 连接变量，仅需列出他们，用空格将他们用**空格**分开
- 叉乘变量，用星号*将变量隔开
- 分组变量，用括号（variable1，variable2，……）将他们括起来

## 美化TABULATE的输出（格式调整）

```SAS
PROC TABULATE FORMAT = COMMA10.0;
```

COMMA10.0表示表中的数字有逗点，但是没有小数位数。

###BOX = '	'和MISSTEXT =  '	'

```SAS
TABLE Region, MEAN*Sales / BOX = 'Mean Sales by Region' MISSTEXT = 'No Sales';
```

BOX和MISSTEXT语句放入TABLE中。

- BOX = 'STR';在每个tabulate的**左上角的空白框**中写入短语，帮助读表的人更好的理解表的内容
  比如：BOX = 'THIS IS A TABULATE ABOUT .................'
- MISSTEXT = 'STR';当有缺失值的时候，SAS以一个点号表示，但是很多人不好理解，所以用MISSTEXT在输出的时候，用一串TEXT来替代。比如MISSTEXT = 'No Sales Data！'

## PROC REPORT生成简单报告

```SAS
PROC REPORT NOWINDOWS MISSING;
	COLUMN variable-list;
	*column用于指定REPORT将会使用哪些变量，缺失的话，默认使用所有变量
```

- NOWINDWS为默认选项，表示不打开交互式报表窗口，否则打开交互式窗口。
- **当COLUMN中只有数值型变量的时候，就会对所在变量进行求和计算。**
- 如果要保留变量中的缺失值，添加MISSING.

### 使用DEFINE语句

- DEFINE用于对单个变量指定特定的选项。
- 一个DEFINE只能指定一个变量，指定多个变量，要使用多次DEFINE语句

```SAS
DEFINE variable / options 'column-header';
```

在变量后添加斜杠和选项名称，并用字符串为其指定相应的选项。

常见选项：

- ACROSS 为变量的每个唯一值创建一列
- ANALYSIS 计算变量的统计量，默认统计量为求和
- COMPUTED 创建新的变量，取值在计算快中定义
- DISPLAY 为数据集中的观测创建一行
- GROUP 为变量的唯一值创建一行
- ORDER 为每个观测创建一行，并按照排序变量取值进行排列

示例代码：

```SAS
DEFINE Age / ORDER 'Age at/Admission';
```

即是让SAS生成的报表按照变量Age排序，并且用Age at Admission作为该变量的列标题，at后的反斜杠表示换行。

示例数据：

```SAS
dinasaur				nm	west	2	6
ellis island			nm	east	1	0
everglades				np	east	5	2
grand canyon			np	west	5	3
great smoky mountains	np	east	3	10
hawaii volcanoes		np	west	2	2
lava beds				nm	west	1	1
statue of liberty		nm	east	1	0
theodore rooservelt		np	.		2	2
yellowstone				np	west	2	11
yosemite				np	west	2	12
```

变量包含名称、类型（np国际公园，nm国家纪念碑）、区域（west、east）、博物馆数量、露营地数量

示例代码：

```SAS
PROC REPORT DATA = natparks NOWINDOWS MISSING;
	COLUMN Region Name Museums Camping;
	DEFINE Region / ORDER;
	DEFINE Camping / ANALYSIS 'Campgrounds';
```

























