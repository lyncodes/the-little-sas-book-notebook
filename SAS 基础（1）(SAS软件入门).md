# SAS 基础（1）(SAS软件入门)

## 目录



[TOC]



本系列文档主要参考自《the little sas book 5th edition中文版

## SAS语法

- sas的每一条语句都以分号结尾

- sas不区分大小写，但是**建议将系统自带关键字用大写，方便人类阅读。**

- sas的注释用`*`或者`/*    */`来进行标记

## SAS 数据集特点
1. 类似于EXCEL，sas的有观测和变量两个概念，分别对应于EXCEL中的行和列。

   - 观测------->行
   - 列----------->列

   但是sas是更加专业的统计软件，概念中更多的是融入了统计的概念。**（观测和变量都是统计概念）**

2. sas中的数据类型
  和其他的编程语言无异，数据类型主要是有数字（numeric）和字符（string）还有逻辑符号（logistic）组成，其余的数据结构都是有这几种基本数据结构自由组合而成。

3. 变量名命名规则
  和C语言，C++类似

  - 变量名长度不超过32个字符
  - 名称以**字母**，**下划线**开始
  - 名称不包含**特殊符号**
  - 字母大小写均可，**SAS不区分大小写，但是我们人类尽量要区分大小写，方便我们阅读代码**
    在实际应用中我们一般均以字母和下划线来构建变量名。
## 一段SAS代码的基本结构
- DATA步

- PROC步

  ```SAS
  DATA distance;
  	Miles = 26.22;
  	Kilometers = 1.61 * Miles;
  PROC PRINT DATA = distance;
  RUN;
  	
  ```

  暂时不用管代码具体意义，我们注意到：

  - 程序有DATA和PROC两个关键字
  - DATA和PROC下面可以**有超过一条语句**
  - DATA步：读取、修改数据
  - PROC步：分析（排序、方差、回归等）、打印等功能

**另外，sas语法是面向过程的！！**

## SAS的窗口和命令

窗口有：

- 编辑器(editor):用来输入，编辑我们的SAS代码
- 日志(log):记录程序运行的详细情况**这非常重要，这能够让我们能够清楚程序是否运行正确**
- 输出(result):sas程序运行的结果，包括表格、图表
- 资源管理器(explorer):包括sas的文件和逻辑库(sas中的逻辑库对应于SQL中的database)

命令有:

- `SUBMIT`,提交代码运行，等效于    **小人按钮**

- `RECALL`,调回以前提交的代码（当你运行了修改后的代码，但是又想看看以前的代码）**有点GIT的味道**

## SAS 日志

一段简单的log如下所示“

```sas
17   DATA distance;
18       miles =  100;
19       kilometers = miles * 1.61;
20   RUN;

NOTE: The data set WORK.DISTANCE has 1 observations and 2 variables.
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      cpu time            0.00 seconds


21   PROC PRINT DATA =distance;
22
23   RUN;

NOTE: There were 1 observations read from the data set WORK.DISTANCE.
NOTE: PROCEDURE PRINT used (Total process time):
      real time           0.04 seconds
      cpu time            0.00 seconds

```

`NOTE: The data set WORK.DISTANCE has 1 observations and 2 variables.`

**NOTE返回有一个观测和两个变量，这和我们的代码是一致的。**

>**匆匆一瞥就可以确保我们没有丢失数据**

```sas
NOTE: DATA statement used (Total process time):
      real time           0.00 seconds
      cpu time            0.00 seconds
```

返回了计算机资源的使用情况，当数据量小的时候没什么关系，当数据量很大的时候就可以找出具体是那一个环节 最慢了，据此来优化数据处理的性能。

##  SAS 数据逻辑库

sas中的逻辑库相当于是SQL中的databse，逻辑库下包含多张表格。

- 当前逻辑库`library`,下辖三个库和其他逻辑库(SAS/GRAPH的MAPS逻辑库，或者自己、同事设置的库）
- SASHELP库，包含SAS回话信息、SAS示例数据集
***
- **WORK库。**是SAS数据的临时存储位置和默认逻辑库，当我们不指定逻辑库的时候，数据就默认保存到WORK库中，不指定保存地址的话，在SAS退出的时候临时的数据就会被删除。
***

- 创建新的逻辑库：
  1. 右键单击资源管理器空白处，点击新建
  2. 输入新建逻辑库的名字（不超过8个字符）
  3. 指定路径，即是逻辑库的保存地址
  4. 同时勾选`启动时启用`,下一次SAS启动的时候会自动加载这个新建的逻辑库

# 下一节我们继续介绍如何将数据导入到SAS中。

