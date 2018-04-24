# SAS 基础（5）(使用输出交付系统（ODS）增强输出)

## 目录

[TOC]

*ODS---Output Delivery System*

ODS决定输出的目标和形式。SAS主要支持以下的输出目标：

- HTML
- LISTING  文本数据
- PDF
- PS  PS脚本
- PRINTER 高分辨率打印
- RTF
- MARKUP 标记语言，包括XML,LATEX,CSV等
- DOCUMENT 输出文档，DOCUMENT可以被转换为PDF，用可以被转换为RTF，可以重复使用，算是一种中介格式
- OUTPUT  输出标准的SAS数据集

## 模板

模板主要有样式模板和表模板

- 表模板，指定输出基本结构（框架），比如那个变量在哪一列
- 样式模板，指定如何显示，如表头是蓝色还是红色

---

## **数据**+**表模板**+**样式模板**=**输出目标**

---

## 追踪、选择  过程步

### ODS TRACE语句

TRACE语句用于通知SAS在日志中打印**输出对象**的相关信息。

```SAS
ODS TRACE ON;
*打开追踪
PROC ---------------
----------
----------
RUN;
ODS TRACE OFF;
*关闭追踪
```

**RUN必须在TRACE OFF之前，因为TRACE是立即执行的，如果先执行TRACE OFF再执行RUN,则不会实现追踪功能。**

### ODS SELECT语句

用ODS SELECT语句**选择特定的输出对象**

语法：

```SAS
PROC ----------------------；
ODS SELECT output-object-list；
RUN;
```

示例代码：

```SAS
PROC MEANS DATA = giant；
	BY Color；
ODS SELECT Means.ByGroup1.Summary;
RUN;
```

对GIANT数据集运行MEANS过程，使用SELECT语句选择第一个输出对象-Means.ByGroup1.Summary。

## 从过程步中输出数据到SAS数据集中

前面有OUTPUT语句可以输出数据到SAS数据集中，现在用ODS OUTPUT命令，**几乎可以将过程步中生成的任意数据保存到SAS数据集中。**

- ODS OUTPUT依赖于ODS TRACE语句

##  创建文本输出

纯文本在SAS中由基本的字符组成，不包含其他特殊的格式，所以可移植性高，打印紧凑的优点。

语法：

```sas
ODS LISTING FILE = '-----PATH-----filename.lst';
ODS NOPROCTITLE;
*删除过程步中的标题
```

## 创建HTML输出

从SAS9.3开始，HTML是SAS的默认输出格式。不必刻意的指出。

语法：

```SAS
ODS HTML BODY = 'body-filename.html' options;
```

options的选项有：

- CONTENTS = 'filename'		创建一个连接到正文的目录文件
	 PAGE = 'filename‘ 			创建一个页码连接的目录文件
- FRAME = 'filename‘                       创建框架文件
- STYLE = style-name                      指定样式模板，**默认是HTMLBLUE**

```SAS
ODS HTML FILE = 'AnnualReport.html' STYLE = BARRETTSBLUE;
```

指定了将结果保存在AnnualReport.html中，并且风格设置为BARRETTSBLUE。

## 创建RTF（Rich Text Format）输出

语法：

```SAS
ODS RTF FILE = 'filename.rtf' options;
```

RTF的options和HTML的options不一样：

- BODYTITLE :正文中的标题和脚注，让标题跟随表格一起出现，而不会出现在WORD的页眉和脚注上
- COLUMN = n：指定每页输出多少列内容
- STARTPAGE =  value 控制分页符
  - value = yes，在过程步之间插入分页符，也就是WORD的一页中会出现多张表格
  - value = no，关闭分页符
  - value = now，将会在当前位置插入分页符
- STYLE = style-name 指定样式模板，默认为RTF

示例代码：

```SAS
ODS RTF FILE = 'AnnualReport.rtf' STYLE = SANSPRINTER;
```

指定输出文件AnnualReport.rtf，指定输出风格为SANSPRINTER。

示例代码：

```SAS
*先创建一个RTF文件
ODS RTF FILE = '-----PATH-----.rtf' BODYTITLE STARTPAGE = NO;
ODS NOPROCTITLE;
********************
DATA 
.
.
.
PROC
.
*中间代码
.
.
.
RUN;
********************
ODS RTF CLOSE;
*最后，生成RTF后要关闭RTF文件
```

## 创建PDF文件

语法：

```SAS
ODS PDF FILE = 'filename.pdf' options;
```

options:

- COLUMN = n 指定每页输出几列内容
- STARTPAGE =value ，和前面一样
  - value = yes，在过程步之间插入分页符，也就是WORD的一页中会出现多张表格
  - value = no，关闭分页符
  - value = now，将会在当前位置插入分页符
- STYLE =style-name: 默认是PRINTER

示例代码：

```SAS
*先创建一个PDF文件
ODS RTF FILE = '-----PATH-----.pdf' STARTPAGE = NO;
ODS NOPROCTITLE;
********************
DATA 
.
.
.
PROC
.
*中间代码
.
.
.
RUN;
********************
ODS PDF CLOSE;
*最后，生成PDF后要关闭PDF文件
```

## 自定义标题（TITLE）和脚注(FOOTNOTE)

语法:

```SAS
TITLE options 'text-str-1' options 'text-str-2' options 'text-str-3'………………
FOOTNOTE options 'text-str-1' options 'text-str-2' options 'text-str-3'………………
```

options有以下选择：

- COLOR:   字体的颜色
- BCOLOR:  字体的背景颜色（背景填充）
- HEIGHT:    设定文本的高度
- JUSTIFY:      设定对齐方式
- BOLD：       加粗显示
- ITALIC:        斜体显示

示例代码：

```SAS
TITLE COLOR=BLACK 'Black' COLOR=GRAY 'Gray' COLOR=LTGRAY 'Light Gray'；
```

将会产生标题如下

							### 			Black（黑色）Gray（灰色）Light Gray（淡灰色）

实际上SAS支持数百种颜色，除了用black、red等名字指定，还可以通过16进制颜色对其进行赋值。

**HEX COLOR的选择，直接在GOOGLE中搜索HEX COLOR，非常好用！！！**

#### 背景颜色BCOLOR=

```SAS
TITLE BCOLOR ='YOUR HEX COLOR' 'THIS IS YOUR TEXT TO BE COLORED!'
```

#### 高度 HEIGHT=

指定文本的高度

```sas
TITLE HEIGHT=12PT 'Small' HEIGHT=.25IN 'Medium' HEIGHT= 1CM 'Large';
```

分别指定了文本Small的大小为12像素,Medium为0.25英寸，Large为一厘米。

#### 对齐方式 JUSTIFY=

```SAS
TITLE JUSTIFY=LEFT 'Left' JUSTIFY=CENTER 'VS.' JUSTIFY=RIGHT 'Right';
```

指定了Left在左侧，vs.在中间，Right在右边。

#### 字体 FONT = 

```SAS
TITLE 'Default' FONT=Arial 'Arial' FONT='Times New Roman' 'Times New Roman' FONT=Courier 'Courier'；
```

可见，默认情况下，不指定字体，SAS会自动指定默认的字体。

给Arial指定了Arial字体，Times New Roman指定了Times New Roman字体，Courier指定了Courier字体。

#### 粗体和斜体BOLD and ITALIC

```SAS
TITLE BOLD 'Bold' ITALIC 'Italic';
```















































