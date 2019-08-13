## 第二章
### 2.1 逻辑库(名称长度最长为8个字符)
* 永久库  
  两种方式：  
 1、菜单栏按钮  
 2、libname  库名 "地址";</br>  

* 临时库(Work)  
  
### 2.2 数据集  
$$
数据集组成部分\begin{cases}
描述信息\\
数据值\\
索引\\ 
扩展信息
\end{cases}
$$
* SAS数据文件  
  查看数据文件的描述信息  
  > proc contents data = 库名.数据文件名;  
  
  查看数据文件的数据值
  > proc print data = 库名.数据文件名;  

  建立数据文件
  > 建永久数据集(临时库名Work省略)  
  data 库名.数据文件名;  
      &emsp;set sashelp.class;  
  run;
* SAS视图  
  建视图</br>
  data step  
  > data 库名.视图名(或 view = 库名.视图名)；  
    &emsp;set sashelp.class;  
  run;  

  proc sql方法
  > proc sql;  
    &emsp;create view 库名.视图名 as  
    &emsp;selelct *  
    &emsp;from sashelp.class;  
  quit;

### 2.3 变量
$$
变量类型\begin{cases}
数字(存储浮点数，包括日期和时间)\\
字符(拉丁字母，0~9阿拉伯数字，其他特殊字符)
\end{cases}  
$$  
SAS日期、时间以及日期时间的本质（类似时间戳，如期记录天数，时间记录秒数，日期时间记录秒数）  
> data tmp;  
>   &emsp;date = "01Jan1960"d;  
>   &emsp;time = "00:00:00"t;  
>   &emsp;datetime = "01Jan1960 00:00:00"dt;  
> run;

输出结果：

|   | date | time | datetime |  
|:-:|:----:|:----:|:--------:|  
| 1 |   0  |   0  |     0    | 

### 2.4  SAS 编程语言  
#### 2.4.1 SAS的程序结构  
(1) 以DATA语句或者PROC语句开头  
(2) 以RUN语句、QUIT语句、新的DATA语句或者PROC语句结束  

#### 2.4.2 SAS语法规则  
(1) 语句语法规则  
(2) SAS名语法规则  
若要使用中文名，需要做如下修改  
> options validmemname = extend validvarname = any;  
> data 中文名称;  
>   &emsp;SAS中文变量名1 = "YES";  
>   &emsp;SAS中文变量名2 = “YES”;  
> run;  

输出结果： 

|   | SAS中文变量名1 | SAS中文变量名2 |  
|:-:| :------------:| :-----------: |  
| 1 | YES           | YES           |     

#### 2.4.3 SAS语言元素  
$$
SAS语言元素
\begin{cases}
SAS语句\\
表达式\\
选项\\
格式\\
函数\\
Call列程
\end{cases}
$$  
* 表达式  
  定义：由一系列操作数和操作符构成的，可执行的，并且产生结果值得序列  
  特殊操作符： （><）取小操作符，（<>）取大操作符  
  运算优先级： 注意+-优先级高于*/  

* 选项  
  系统选项、数据集选项

* 格式  
  
* 函数与Call例程  
  (后续章节讨论)

  综合实例
  
#### 2.4.4 三种逻辑结构  
* 顺序结构  
* 选择结构  
  >例：  
  >data male female;  
  >   &emsp;set sashelp.class;  
  >   &emsp;  &emsp;if sex = "M" then output male;  
  >   &emsp;  &emsp;else if sex = "F" then output female;  
  >   &emsp;  &emsp;else put "Invalid sex :" sex;  
  >run;  
  </br>
  另外如果想执行的不仅仅是一个动作，可以加入夹板语句DO-END  
  >data male female;  
  >   &emsp;set sashelp.class;  
  >   &emsp;  &emsp;if sex = "M" then do; gender = "Male"; output male; end;  
  >   &emsp;  &emsp;else if sex = "F" then do; gender = "Female"; output female; end;  
  >   &emsp;  &emsp;else put "Invalid sex :" sex;  
  >run;  

* 循环结构  
$$
\begin{cases}
DO循环\\
DO-WHILE循环\\
DO-UNTIL循环\\
\end{cases}
$$  
(1) DO循环  
> 例:   
> data schedule;  
>   &emsp;do date = "01Sep2016" to "30Sep2016";  
>   &emsp;day = weekday(date);  
>   &emsp;&emsp;if day in (1, 7) then Activity = "Running" ;  
>   &emsp;&emsp;else if day in (2, 4, 6) then Activity = "Writing" ;  
>   &emsp;&emsp;else Activity = "Reading";  
>   &emsp;&emsp;output;  
> run;  

(2) DO-WHILE循环  
> 例：  
> data dowhile;  
>   &emsp;i = 0;  
>   &emsp;do while(i<5);  
>   &emsp;&emsp;i + 1;  
>   &emsp;&emsp;output;  
>   &emsp;end;  
> run;

(3) DO-UNTIL循环  
> 例：  
> data dountil;
>   &emsp;i = 0;  
>   &emsp;do until(i>=5);  
>   &emsp;&emsp;i + 1;  
>   &emsp;&emsp;output;  
>   &emsp;end;  
> run;

#### 2.4.5 数组结构   
SAS编程语言里，数组是一系列有特定顺序的变量组成的一个临时变量组。数组中的变量必须有相同的数据类型。  
(1) 定义数组  
> ARRAY array-name {number-of-elements} <$> &lt;length&gt; &lt;array-elements&gt; &lt;(initial-value-list)&gt;;  
{*}表示自动计数，&lt;&gt;中元素表示并非必须有

> 例：  
> array bp{2, 7} sub1-sub7 dbp1-dbp7 (163 164 165 164 171 170 154 99 92 94 95 94 98 98);  

(2)数组访问 db{m, n}  

#### 2.4.6 函数与CALL例程  
