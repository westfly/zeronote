# AWK实战 #
## **概况** ##
AWK是一种优良的文本处理工具。它不仅是 Linux 中也是任何环境中现有的功能最强大的数据处理引擎之一。
名字的来源于 Aho、(Peter) Weinberg和(Brain)Kernighan 三个发明人的首字母。
awk语法：
与其它UNIX命令一样，awk拥有自己的语法：
```shell
awk [ -F re] [parameter...] ['prog'] [-f progfile][in_file...] 
```
awk 的应用场景基本是是报表类的统计需求。使用 awk 主要有认识到两个
1. 切分字段
2. 对字段进行操作。

awk基本上是写起来很方便，很容易懂，基本上照着例子练习给一个小时，80%的需求都可以cover住。
在使用awk的过程中，感觉awk写的代码很难复用，处理结构化的数据时比较费力，在某些情况下，为了切分字段的方便，还需要和sed/cut等shell命令通过管道结合在一起使用。




## **变量** ##

### **内置变量** ###
V 变量 含义 缺省值
--------------------------------------------------------
N ARGC 命令行参数个数 

G ARGIND 当前被处理文件的ARGV标志符

N ARGV 命令行参数数组

G CONVFMT 数字转换格式 %.6g

P ENVIRON UNIX环境变量

N ERRNO UNIX系统错误消息

G FIELDWIDTHS 输入字段宽度的空白分隔字符串

A FILENAME 当前输入文件的名字

P FNR 当前记录数

A FS 输入字段分隔符 空格

G IGNORECASE 控制大小写敏感0（大小写敏感）

A NF 当前记录中的字段个数

A NR 已经读出的记录数

A OFMT 数字的输出格式 %.6g

A OFS 输出字段分隔符 空格

A ORS 输出的记录分隔符 新行

A RS 输入的记录他隔符 新行

N RSTART 被匹配函数匹配的字符串首

N RLENGTH 被匹配函数匹配的字符串长度

N SUBSEP 下标分隔符 "\034"


#### 参数变量 ####
#### 命令行变量 ####
获取
```shell
#!/bin/bash
awk 'BEGIN {
print ARGC
for(i=0;i<ARGC;i++)
 print ARGV[i];   #依次印出awk所记录的参数
}' $*
# argv[0] 代表程序名
# argv[1] ~~argv[n]代表参数,
```
**注意：**参数以空格分隔，如果通过变量传递的话，某些带空格的变量可能被拆分为多个参数。所以变量最好添加引号。


----------


## **函数** ##
之前提到awk不容易复用，但awk提供了系统库函数供调用。
也支持自定义函数，但支持得比较有限。

### 参数传递 ###
### 返回值 ###
### **内置函数** ###

## **系统调用** ##
### 参数传递 ###

shell变量 传递给awk变量的方式有三种：

* -v 的方式传递

```shell
awk -v deploy=$deploy '{print deploy}'
```

* 直接引用

```shell
awk '{print "'$deploy'"}'
```
* 其它方式

## **其它** ##
处理多个文件，有两种方案
```shell
awk 'NR==FNR{...}NR>FNR{...}' file1 file2
awk 'NR==FNR{...;next}{...}' file1 file2
```
这种方式适用于，第一个文件作为dict的情况。
```shell
awk 'FILENAME=="file1"{...}
     FILENAME=="file2"{...}
     FILENAME=="file3"{...}...' file1 file2 file3
awk 'FILENAME==ARGV[1]{...}
	 FILENAME==ARGV[2]{...}
     FILENAME==ARGV[3]{...}...' file1 file2 file3
awk 'ARGIND==1{...}
     ARGIND==2{...}
     ARGIND==3{...}... ' file1 file2 file3 ...
```
用得比较多的是第一种。


## Reference ##
* http://www.catonmat.net/download/awk.cheat.sheet.pdf
* http://www.catonmat.net/blog/awk-one-liners-explained-part-two/
* http://backreference.org/2010/02/10/idiomatic-awk/
* http://www.catonmat.net/blog/ten-awk-tips-tricks-and-pitfalls/
