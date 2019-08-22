## 第三章 读取数据
### 3.1 读取对象与读取方式  
$$
外部数据文件\begin{cases}
数据库管理系统(MySQL等)\\
单机文件(Excel，SPSS等)\\
平面文件(TXT，csv等)\\
流式数据(datalines语句后面的数据行)
\end{cases}
$$

$$
SAS读取方式\begin{cases}
LIBNAME语句(SAS获取外部数据库文件最快速、直接的方式)\\
SQL\\
ACCESS/DBLOAD过程(已不再推荐)\\
IMPORT/EXPORT过程\\
INFILE+INPUT(DATA步编程方式读取)\\
IO函数
\end{cases}
$$

### 3.2 数据读取策略


### 3.3 读取DBMS数据文件 
#### 3.3.1 SAS/ACCESS 与DBMS 
> libname mylib sql_server_name user= password=“” datasrc=数据库名称;  

* 另附ODBC接口方法  
   可以通过该方法连接远程服务器上的数据库，通过该方法成功连接阿里云服务器上mysql数据库  
步骤：  
(1)下载并安装MySQL ODBC 8.0 ANSI Driver(本人服务器上安装的为5.7版本mysql,该驱动适用于5.5,5.6,5.7,8.0,具体参照官网)  
(2)控制面板配置ODBC,[过程参照链接](https://cloud.tencent.com/developer/news/243736)  
(3)SAS连接  
> libname mylib libname mylib odbc datasrc=mysql_lib user= password = "";  
> datasrc=自己(2)中添加使用的名称

### 3.4 读取PC数据文件
* [PROC IMPORT方法](https://documentation.sas.com/?docsetId=proc&docsetTarget=n1qn5sclnu2l9dn1w61ifw8wqhts.htm&docsetVersion=9.4&locale=en)
> PROC IMPORT  
> DATAFILE = " "  
> &lt; DBMS=.文件扩展名(例如DBMS=.sav) &gt;  
> &lt; OUT=库.dataset &gt;  
> &lt; 数据筛选等操作语句 &gt;  
> &lt; REPLACE &gt;/* 重复运行时替换掉同名数据集 */  
> &lt; 更详细的读取设置 &gt;  
> ;  
> RUN;
* LIBNAME访问PC文件(不深入学习，直接建议使用PROC IMPORT 方法)

> 例：(不要使用xls格式)  
> filename myexel "D:\";/\*文件快捷方式*/  
>proc import  
>out = myxlsx1 datafile = myexcel dbms = excel repalce;  
>run;  
>*===  
>proc import out = myxlsx2   
>datafile = myexcel  
>dbms = xlsx  
>replace;  
>range = "sheet1$A1:K20";/\*指定范围\*/  
>getnames = yes;/\*第一行当列名\*/  
>run;  
>*===  
>proc import out = myxlsx3  
>datafile = myexcel  
>dbms = excel  
>replace;  
>dbdsopts = "firstobs=3 obs=8";/\*指定行数范围第3行到第8行\*/  
>getnames = yes;  
>run;  

### 3.5 读取Flat数据文件
Flat最常见的就是csv和txt文件  
#### 3.5.1 读入csv文件
csv也可以用前面读取Excel文件的方式读取，不过可以直接用guessingrows来指定通过前多少行确定变量类型、用datarow来指定数据从哪一行开始  
>filename mycsv "D:\";  
>proc import out = mycsv1  
>datafile = mycsv   
>dbms = csv  
>replace;  
>getnames = yes;  
>guessingrows = 5;  
>datarow = 3;  
>run;  

#### 3.5.2 读入TXT特殊字符分割的文件  
proc import语句  
修改和添加：
>dbms = dml  
>delimiter = '分隔符';  
>delimiter = '20'x; /\*表示空格\*/  
>delimiter = '09'x; /\*表示制表符\*/  

### 3.6 读取流式数据
#### 3.6.1 流式数据初探
>data trail;  
>   &emsp;do group = "T", "C";  
>   &emsp;&emsp;do Survive = "Yes", "No";  
>   &emsp;&emsp;&emsp;input freqs@@; /\*双尾，读一个数据就换行\*/  
>   &emsp;&emsp;&emsp;output;  
>   &emsp;&emsp;end;  
>   &emsp;end;  
>datalines;  
>95 5 90 10  
>;  
>run;

#### 3.6.2 INPUT语句一般语法
>INPUT &lt;输入申明，可以对包括变量，指针，列位置，格式修饰符以及输入格式等做进一步说明&gt; <@|@@>;  

#### 3.6.3 列表读入式  
* input @3 name;  /* 表示下面读取数据时，从第三个位置开始 */  
* input name :$13. ; /* 表示碰到空格或者第十三列再读完name的数据，冒号表示读入的变量列上不齐整，读到空列、读完字符定义的长度或者数据行结尾时就算一个变量读完 */  
* input name & $ ;  /* 表示碰到第一个空格不会认为此变量结束，还会结束 */  
* input name : & $13. ;  /* 可以组合使用 */  
* 其他分隔符数据  
  > data tmp;  
  >   &emsp;infile datalines delimiter = ',' ;  
  >   &emsp;input name $ gender$ age location $ affiliation $12. ;  
  >   &emsp;datalines;  
  >   &emsp;HQ Gu,M,30,Beijing,"PUMC,CAMs"  
  >   &emsp;;  
  > run;  

  |   | name | gender | age | location | affiliation |  
  |:-:| :---:|:------:|:---:|:--------:|:-----------:|  
  | 1 | HQ Gu| M      | 30  | Beijing  | "PUMC,CAMs" |  

#### 3.6.4 列读入式  
>INPUT variable<$> start-column<-end-column><.decimals><@|@@>;  

#### 3.6.5 格式读入式  
参考3.6.3  

#### 3.6.6 命名读入式
>INPUT variable= <$> start-column<-end-column> <.decimals> <@|@@>;  

>data tmp;  
>input name= $;  
>datalines;  
>name=SP  
>;  
>run;

#### 3.6.7 DATALINES数据综合实例  

#### 3.6.8 关于列表、指针及格式等  

#### 3.7 数据导出  
>PROC EXPORT DATA=SAS数据集  
>OUTFILE =   
>DBMS =  
>LABEL  
>replace;  
>RUN;  