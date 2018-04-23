# SAS 基础（2）(导入数据到SAS)

## 目录

[TOC]
## SAS导入数据的哲学思想
> SAS不同于其他软件的地方在于其异常灵活的导入数据的方式、
>
> > 1. 其他简单的数据导入方式一般以逗号、Tab来作为分隔符
> >
> > 2. 而SAS的思想在于**指针**，和C语言的的**指针**是一个思想
> >
> >    > 这和SAS是用C语言开发的原因不谋而合呢？
> > 3. 我形象的将**指针的思想**理解为**指哪儿打哪儿**，通过不断移动的指针，来决定SAS从数据集中读取的内容

## 导入数据的4中基本类别

1. 直接输入数据来创建SAS数据集

   * 很原始的办法，敲出来的代码中，就包含了要创建的SAS数据集，比如：

     ```sas
     DATA USpresidents;
     	INPUT President $ Party $ Number;
     	DATALINES;
     Adams F 2
     Lincoln R 2
     Grant R 18
     Kennedy D 35
     ;
     RUN;
     *这代码中就包含了要创建的SAS数据集中的基本元素。
     ```

     做演示示例是合适的，但是要导入大规模数据，这显然是不可能的。

2. 利用原始数据文件来创建SAS数据集

   - 从原始的`文本文件`、`ASCII文件`、或者其他原始文件中导入数据到SAS中。
   - 典型的包括CSV(comma seperated values)文件，或者其他用空格、Tab作为分隔符的文件
   - 或者是没有分隔符的原始文本文件，用SAS的指针在文本中      **游走**         

3. 将其他软件的数据文件转换成SAS数据集

   SAS的IMPORT命令可以导入非常多其他软件数据格式，包括有

   - Microsoft Excel

   - IBM Lotus

   - dBASE

   - Stata

   - SPSS

   - JMP

   - Paradox

   - Microsoft Access、

     当然了，以上功能需要安装SAS/ACCESS Interface to PC Files模块！！！

4. 直接读取其他软件的数据文件

   直接读取其他数据库的文件

   - ORACLE
   - DB2
   - INGER2
   - MYSQL
   - SYBASE
     **更多关于直接读取数据库的信息，参考SAS帮助文档。**




## 导入向导读取文件

用导入向导来导入数据非常方便，只要点击file>import data即可选择相应文件的**类型**和**路径**

指定**分隔符**和**从哪里开始读取**

## 着重介绍如何从原始文本文件中读取数据

> 因为在实际的应用中
>
> > 1. 数据的来源、种类都及其复杂
> > 2. 数据的完整度、复杂性也良莠不齐
> > 3. 往往需要从杂乱无章的原始文本中提取需要的数据，SAS的**指针**功能就排上了大用处

### 读取    内部    数据集（即是手敲数据）
```sas
DATA USpresidents;
	INPUT President $ Party $ Number;
	DATALINES;
Adams F 2
Lincoln R 2
Grant R 18
Kennedy D 35
;
RUN;
```


DATALINES在DATA步的最后一行，表示后面的内容是程序的内部数据

### 读取    外部     数据集，即是从指定路径的外部文件中读取文件

读取外部文件，用`INFILE`指令

`INFILE`指令指定外部文件的**路径**和**读取数据的选项**

```sas
DATA uspresidents;
	INFILE 'c:\myrawdata\president.dat';     
	*指定文件路径
	INPUT president $ party $ number;
RNN;
```



- INFLIE必须在DATA语句之后
- INFILE必须在INPUT语句之前

`INFILE 'C:\myrawdta\president.dat' LRECL=2000`

- LRCEL即是length of record ，可以手动设置为最大记录长度为2000或者其他数值，默认为256。

### 读取    空格分隔    的原始数据

#### 列表输入

适用条件：

* 原始文本中所有值都被**至少一个空格**分开

```SAS
a b c d e
OR
a    b    c    d
```
局限所在：

- 必须读取文件中的所有数据，**不能跳过不需要的值**

- 任何缺失的值都要用**句点**来表示

- 字符型数据**没有内嵌空格**（因为如果有内嵌空格，则SAS认为这是两条信息）

- 默认的字符长度是8个字符，可以用LENGTH来指定具体的字符长度（1-32767）


典型的**列表输入**：

``` SAS
INPUT Name $ Age Height;

*Name后跟的  $  美元符号，表示Name是字符型变量，相对的Age、Height则是数字形变量，中间只需空格，而不需 $
```

看一个例子：

```SAS
lucky 2.3 1.9 . 3.0    *有一个缺失值，但是******列表输入*****按照变量个数，将缺失值也读取进去
spot 4.6 2.5 3.1 .5   *最中规中矩的输入格式，没有缺失，没有混乱
tubs 7.1 . . 3.8    *两个缺失值，同上
hop 4.5 3.2 1.9 2.6   *中规中矩
noisy 3.8 1.3 1.8    *****注意到有一个数值   跳到   下一行了，但是没有关系，SAS的列表输入仍然按照
1.5                  ***变量个数，跳到下一行读取1.5，并存储在noisy这个观测中
winner 5.7 . . .     *三个缺失，同上
```

读取上述数据的SAS代码：

```sas
DATA todas；
	INFILE '-----PATH------\ToadJump.dat';
	INPUT Toadname $ weight jump1 jump2 jump3; 
    *一个字符型变量，4个数字形变量
RUN;
PROC PRINT DATA =  toads;
	TITLE =  'SAS DATA SET TOADS'     
	*TITLE设置打印的      表头
RUN;
```

### 读取    按列排列    的原始数据

原因:**一些文件，没有空格作为分隔符**

为什么没有空格？

因为空格也是要占存储空间的，所以如果所有数据的位置是规律的，我们就可以规律的把他们读取出来，而不用让空格来充当间隔符。

- 要求每个变量都能在数据行的**相同位置**找到

- 所有值都是**字符型**或者**标准数值型**

#### 列输入的优势

- 值与值之间**无需空格**，（极大的节省了存储空间，尤其是数据及其庞大的时候）
- 缺失值可以留空（为什么，因为我们不用空格来做间隔符啊！！！）
- 字符型数据可以内嵌空格（同样是因为我们不需要用空格来做间隔符）
- 可以跳过不需要的变量（同样，我们显示的指定读取的位置，**不需要依赖于原始文本的结构）

> 这里，已经可以总结出来一点点东西了：
>
> 列表输入：比较依赖于原始文本的结构，依靠间隔符来界定变量
>
> 列输入：只需要了解原始文本的结构，显示的指定需要读取数据的范围（也就是说，我想读取10-15个字符，再读取18-20个字符，这是自由的，还能够跳过不想读的16-17个字符！！！！）非常灵活！！

```SAS
INPUT Name $ 1-10 Age 11-13 Height 14-18;
```

表明：

- Name在1-10位，是字符型变量
- Age在11-13位
- Height在14-18位

列输入示例代码：

```SAS
DATA sales;
	INFILE '------PATH------';
	INPUT VistingTeam $ 1-20 ConcessionSales 21-24 BleacherSales 25-28 OurHits 29-31 
		TheirHits 32-34 OurRuns 35-37 TheirRuns 38-40;
RUN;

PROC PRINT DATA = sales;
	TITLE 'SAS DATA SET SALES';
RUN;
```

列输入原始文本数据：

这些字符型变量个数值型变量分别分布在上述代码所限定的范围内（1-20    21-24   25-28   29-31 32-34  35-37  38=40）

```SAS
columbia peaches     35 67 1 10 2  1
plains peanuts      210    2 5  0  2
gilroy garlics      151035 12 11 7 6
sacramento tomatoes  124 85 15 4 9 1
```

**由于是按照范围来读取数据，所以有不有空格，有不有间隔符也就没有什么关系了**

### 读取    非标准格式     的原始数据

caution：

> 由于是   非标准格式     ！！！ 所以就由我们来指定     格式     ！！
>
> 这里的   格式  是指有标准的间隔符或者相同类型的数据都在相同的位置上，所以需要我们人为的设置格式

示例代码：

```SAS
INPUT Name $10. Age 3. Height 5.1 BirthDate MMDDYY10.;
```

> 和前面直接指定读取范围不同
>
> > 非标准格式按照   递增    的方式进行数据的读取

- Name从第一列开始，读取10位，即1-10位是Name
- Age    则从11位开始，读取3位，即11-13位是Age
- Height则是从14位开始，读取5位，即14-18位是Height，小数点后的1，**表示Age变量保留小数点后一位的精度**
- BirthDate 则采用标准的 MMDDYY10.格式,即month、day、year，一共10位，如10-28-2018

示例原始数据：

```SAS
alicia grossman  13 c 10-28-2012 7.8 6.5 7.2 8.0 7.9
matthew lee       9 d 10-30-2012 6.5 5.9 6.8 6.0 8.1
elizabeth garcia 10 c 10-29-2012 8.9 7.9 8.5 9.0 8.8
…………………………
```

示例读取代码:

```SAS
DATA contest;
	INFILE '-----PATH-----';
	INPUT Name $16. Age 3. +1 Type $1. +1 Date MMDDYY10.(Score1 Score2 Score3 Score4 Score5) (4.1);
	*+1表示跳过一位
	RUN;
PROC PRINT DATA = contest;
	TITLE = 'PUMPKIN CARVING CONTEST';
RUN;
```

- +1表示跳过一位，这是看到的年龄和type中间有一个空格的原因
- `(Score1 Score2 Score3 Score4 Score5) (4.1)`,是因为Score1-5有相同的数据格式，均采用4.1

### 用  混合输入样式  读取数据

示例原始数据：

```SAS
yellowstone			 ID/MT/WY 1872  4,065,493
everglades			 FL 1934        1,398,800
yosemite			 CA 1864          760,917
great smoky mountains NC/TN 1926       520,269
Wolf trap farm		 VA 1966               130
```

示例代码：

```SAS
INPUT ParkName $ 1-22 State $ Year @40 Acreage COMMA9.;
```

- ParkName采用列输入，也就是制定读取范围的头尾
- State、Year采用列表输入，也就是按照空格作为分隔符来读取文本
- Acreage则是才用COMMA9.格式化（删除内部的逗号）
- **@40**     表示把指针移动到第40位！！！！！      即     @n    表示将指针移动到第 n 位！

### 读取     杂乱    的原始数据

#### 列指针    @‘character’

> 当无法确定   @n  中具体的  n 值，但是所需要的数据总是在某个特定的字符串之后，就要使用@‘character’

 `INPUT @'Breed:'DogBreed $;`

代码适用于，数据排列并不整齐，但是狗的品种信息总是出现在‘Breed：’之后，这样可以便可以提取出相应的狗的品种、

#### 冒号修饰符

> 当字符串的<=8的时候，运行良好
>
> > 当字符串的长度>8时，需要显示的指定需要读取的字符串长度
> >
> > > 但是指定长度用$20，会往后读取20个字符（包括空格）
> > >
> > > > 所以，我们需要使用**冒号运算符**

`INPUT @'Breed:'DogBreed :$20.;`

示例原始数据：

```sas
My dog Sam Breed: Rottweiler Vet Bills: $478
```

**加上冒号后，虽然是定义20个字符，      但是遇到了第一个空格之后      ，就完成了此变量的数据读取！！！**

### 一个观测-----------多行原始数据

> 有的时候，一个观测的原始数据**分散**到了多行原始数据中

所以我们需要控制   **SAS指针何时换行**

行指针：

1. `/`斜杠表示跳到下一行
2. `#n`表示跳到原始数据的第n行，#3就是跳到第3行

示例原始数据：

```SAS
Nome AK
55 44
88 29
Miami FL
90 75
97 65
Raleigh NC
88 68
105 50
```

示例代码：

```SAS
INPUT City $ State $ 
	/ NormalHigh NormalLow
	#3 RecordHigh RecordLow;
```

- **该代码读取了9条记录，形成了3个观测**
- `/`让指针在读取完City和State后向下移动一行
- `#3`让指针移动到   第三行

### 多个观测-----------一行原始数据

无独有偶，存在**多行一个观测**，就有**一行多个观测**

> 双尾@@写法

示例原始数据：

```SAS
Nome AK 2.5 15 Miami FL 6.75
18 Raleigh NC . 12
```

示例代码：

```SAS
INPUT City $ State $ NormalRain MeanDaysRain @@;
```

> 清楚的注意到第二个观测  **撕裂**  在了第一行 和第二行，并不是标准的一行一个观测

- @@表示不要开始一个新的观测，换行再读取数据，仍然保存到当前观测中
- 直到读取完毕！！

### 读取原始数据文件的一部分

`@`符号，用于结束INPUT语句

示例原始数据：

```SAS
freeway 408							 3684 3459
surface Martin Luther King Jr. Blvd. 1590 1234
surface Broadway					 1259 1290
surface Rodeo Dr.					 1890 2067
freeway 608							 4583 3860
freeway 808							 2386 2518
surface Lake Shore Dr.				 1590 1234
surface Pennsylvania Ave			 1259 1290
```

示例代码：

```SAS
INPUT Type $ @;
IF Type = 'surface' THEN DELETE;
INPUT Name $ 9-38 AMTraffic PMTraffic;
```

原始数据包含：

- 街道类型
- 街道名称
- 早晨平均每小时通过的车辆数量
- 晚上平均每小时通过的车辆数量

代码：

- 一共有两条`INPUT`语句，第一条用于读取`Type`变量，第二条用于读取`Name\AMTraffice\PMTraffic三个变量`
- `@`表示停留在这个地方，不要继续往后读取数据了
- 接着，就在`IF…………THEN………………`中进行条件的判断，当Type是surface后，就不理会，也就是说  剔除  了surface类型的  observation
- 然后才开始读取后面的观测

运行的LOG：

```SAS
 NOTE: 数据集 WORK.FREEWAYS 有 3 个观测和 4 个变量。
 NOTE: “DATA 语句”所用时间（总处理时间）:
       实际时间          0.01 秒
       CPU 时间          0.01 秒
```

一共8行数据，只有3个观测，其余的5个数据都被DELETE了，这让我们读取我们需要读取的内容。

### INFILE命令的选项

对INFILE命令添加额外的选项，可以是我们对文本的读取更加    精细    。

- FIRSTOBS = N,顾名思义，告知SAS从第N行开始读取原始文本文件。应用于文件头部对文件有所描述的情况

  ```sas
  Ice-cream sales data for the summer
  Flavor Location Boxes_sold
  Chocolate 213 123
  Vanilla 213 512
  Chocolate 415 242
  ```

  ```SAS
  INFILE '-------PATH------' FIRSTOBS = 3
  *让SAS从第三行开始读取
  ```

- OBS=N,指定SAS读取到第N行时停止。（并不是读取N个观测，这个我们在前面已经讨论过了，一行可能对应多个观测，也有可能多行对应一个观测！！！）

-  MISSOVER:**为没有被赋值的变量分配缺失值**

  示例数据：

  ```SAS
  Nguyen	89 76 91 82
  Ramos	67 72 80 76 86
  Robbins	76 65 79 
  ```

示例代码:

```SAS
INFILE '-----PATH------' MISSOVER;
INPUT Name $ Test1 Test2 Test3 Test4 Test5;
```

结果会为Nguyen分配一个点号（缺失值）

为Robbins分配两个点号。

- TURNOVER:

  示例数据源:

  ```SAS
  Jhon Garcia 	114 Maple Ave
  Sylvia Chuang  1302 Washington Drive
  Martha Newton 	45 S.E. 14th St.
  ```

  示例代码：

  ```SAS
  INFILE '-----PATH----' TRUNOVER;
  INPUT Name $ 1-15 Number 16-19 Street $ 22-37;
  ```

  注意到Street变量长度不一而且中间带有空格，所以TRUNOVER的作用在于：

  - 直到遇到了数据行的结尾

  - 或者遇到 格式、列范围指定的最后一个列

    才进行换行。




## 使用DATA步读取分隔文件

> 相当一部分文件的结构是由分隔符对数据进行分割的。
>
> 最常见的有逗号(comma)和制表符(tab)

### DLM=‘	’

DLM即是Delimiter（分隔符），我们可以将其指定为任意的字符。

示例数据：

```SAS
Grace,3,1,5,2,6
Martin,1,2,4,1,3
Scott,9,10,4,8,6
```

示例代码：

```SAS
INFILE '------PATH-----' DLM = ','
```

这里显示的指定分隔符为`,`,遇到其他的分隔符也可以指定其他字符如`DLM='@'`等等。

**如果分隔符是TAB,则用`DLM='09'X`表示，`'09'X`在ASCII中表示tab。**





### DLMSTR='	'

DLMSTR适用于分隔符是字符串的形式，其余同上。

### DSD选项(delimiter sensitive data)

> 当有连续两个分隔符相邻，也就是说两个分隔符之间没有数据，那么就是缺失值。

示例数据：

```SAS
Washington,4,6,1,,7,8
*注意到有一个缺失值
```

示例代码：

```SAS
INFILE '-----PATH-----' DLM=',' DSD;
```

这样会把1和7之间的缺失值用点号`.`来代替。

## IMPORT 命令

SAS其实为我们提供了一个高度智能化的导入功能，用IMPORT语句就可以实现。

```sas
PROC IMPORT DATAFILE = '-----PATH----' OUT = data-set-name;
```

filename是要读取的文件的名称，data-set是要创建的表格的名称。

- 如果已经有一个和当前data-set名字相同的数据集，并且要对其进行覆盖，使用**REPLACE**命令

- 如果文件只包含数据，没有标题，则要使用**GETNAMES=NO**命令，将会为数据集自动分配变量

```SAS
PROC IMPORT DATAFILE = '----PATH----' OUT = data-set REPLACE;
	GETNAMES = NO;
```

## 使用IMPORT读取EXCEL文件

```SAS
PROC IMPORT DATAFILE = '-----PATH-----' OUT = data-set
	DBMS = identifier REPLACE;
```

- DBMS用于指定**标识符**

> 1. DBMS = XLS(用于较老的EXCEL文件)
> 2. DBMS = XLSX(用于较新的EXCEL文件)

### 指定读取EXCEL的某个sheet 和读取区域

```SAS
PROC IMPORT DATAFILE = '-----PATH-----' OUT = data-set
	DBMS = identifier SHEET = "sheet-name" REPLACE;
```

- 指定读取excel中的某个sheet

```SAS
RANGE = "sheet-name$UL:LR";
```

- UL:upper-left,即是读取区域的左上角

- LR：lower-right，即是读取区域的右上角

- 当某一列的数据内部既含有数字也含有字符

  用`MIXED = YES;`

## 列出SAS数据集的基本自我描述

```SAS
PROC CONTENTS DATA = data-set;
```

将会输出冠以此数据集的很多相关基本描述，包括：

- 数据集名称
- 观测数
- 变量数
- 创建日期
- 数据集标签
- …………………………








