---
layout: post
title:  文本三剑客之awk
date:   2017-09-6 10:00:00
categories: Linux
tags: awk 
---



## 简介

> &emsp;&emsp;   awk是一种编程语言，用于在linux/unix下对文本和数据进行处理。数据可以来自标准输入、一个或多个文件，或其它命令的输出。它支持用户自定义函数和动态正则表达式等先进功能，是linux/unix下的一个强大编程工具。它在命令行中使用，但更多是作为脚本来使用。awk的处理文本和数据的方式是这样的，它逐行扫描文件，从第一行到最后一行，寻找匹配的特定模式的行，并在这些行上进行你想要的操作。如果没有指定处理动作，则把匹配的行显示到标准输出(屏幕)，如果没有指定模式，则所有被操作所指定的行都被处理。awk分别代表其作者姓氏的第一个字母。因为它的作者是三个人，分别是Alfred Aho、Brian Kernighan、Peter Weinberger。gawk是awk的GNU版本，它提供了Bell实验室和GNU的一些扩展。下面介绍的awk是以GUN的gawk为例的，在linux系统中已把awk链接到gawk，所以下面全部以awk进行介绍。

> 本文将从以下几个方面来介绍
> 

## 1、awk的命令格式及选项
---

#### 1.1 awk的使用格式

> (1) 命令行格式
>	
> &emsp;&emsp; awk [options] var=value '[ BEGIN{ action;… } ] pattern{ action;… } [ END{action;… } ]' file ...	
>	
> (2) 调用自定义脚本	
> 	
> &emsp;&emsp; awk [options] var=value -f programfile file…	
> 	
**上面说明了awk命令的格式，先看下面的awk命令**

> &emsp;&emsp; awk -F: -v var=value 'BEGIN{print hello } /root/{printf "%-15s %i\n",$1,$3} END{print byebye}' /etc/passwd

```

hello				# 打印BEGIN中的内容
root            0		# 通过/root/匹配查找到的包含root的用户
operator        11		# 通过/root/匹配查找到的包含root的用户
byebye				# 打印END中的内容

```

> 上面awk说明：
> - -F: :指定文件作用行的分隔符
> - -v var=value :用户自己在awk中自己指定的变量
> - BEGIN{  } :可选的语句块，awk开始从输入流中读取行之前被执行，比如变量初始化、打印输出表格的表头等语句通常可以写在BEGIN语句块中
> - END{  } :可选的语句块，在awk从输入流中读取完所有的行之后即被执行，比如打印所有行的分析结果这类信息汇总都是在END语句块中完成
> - pattern{ action;… } : pattern语句块中的通用命令是最重要的部分，也是可选的。如果没有提供pattern语句块，则默认执行{ print }，即打印每一个读取到的行，awk读取的每一行都会执行该语句块
> - printf :awk的输出格式定义，这里的printf与C语言中的用法基本相同，通过选项指定以特定的格式输出，接下来会详细说明
> - $# ,由分隔符分隔的字段（域）标记$1,$2..$n称为域标识。以下会进行说明，这里先了解一下就好

> **awk把每一个以换行符结束的行称为一个记录**
> **记录分隔符：默认的输入和输出的分隔符都是回车，保存在内建变量ORS和RS中**

> 相信通过上面的awk命令，已经能够大致了解awk的用法及格式，下面我们就来详细的介绍一下

#### 1.2 awk的选项

> awk的选项[options]比较简单：
>
> &emsp;&emsp; -F fs : 指定输入文件折分隔符，fs(field separator)是一个字符串或者是一个正则表达式，如-F:

> &emsp;&emsp; -v var=value : 用户定义一个变量并赋值，在awk当中可用

> &emsp;&emsp; -f programfile : 从脚本文件中读取awk命令 

> 以上是awk较为常用的命令选项，还有更多选项这里不再过多介绍

## 2、awk的模式
---

#### awk的模式匹配

> awk中的模式与sed差不多，都是需要通过设定的模式来定位到某个位置，或则是匹配到某个参数。

> 下面介绍一下awk数据处理的模式有哪些

> - 正则表达式，格式为/regular expression/
> - expresssion: 表达式，其值非0或为非空字符时满足条件
> - 指定的匹配范围，格式为pat1,pat2
> - BEGIN/END：特殊模式，仅在awk命令执行前运行一次或结束前运行一次
> - 空模式: 匹配任意输入行

> - (1)/regular expression/：仅处理能够模式匹配到的行，需要用/ /括起来
> &emsp;&emsp;	awk '/^UUID/{print $1}' /etc/fstab
> &emsp;&emsp;	awk '!/^UUID/{print $1}' /etc/fstab

```

[root@Centos6 ~]#awk '/^UUID/{print $1}' /etc/fstab

UUID=ef20e35d-b3b0-4bb0-a7f0-b6da5f9478ea
UUID=bad2ae77-157a-4e40-a1c0-8d67af3cc105
UUID=90739410-fd0f-4419-900a-2b981300f2d0
UUID=97172ba1-b115-4e5a-b739-8f2b2b309115

```
> 命令将包含UUID的行找到并显示出来

> - (2)expression: 关系表达式，结果为“真”才会被处理
> &emsp;&emsp; 真：结果为非0值，非空字符串
> &emsp;&emsp; 假：结果为空字符串或0值
> 如：$1 ~ /root/ 或 $1 == "wang"，用运算符~(匹配)和!~(不匹配)。
> awk '!0{print $1}' /etc/issue 

``` 

[root@Centos6 ~]#awk '!0{print $1}' /etc/issue 
CentOS
Kernel

```
上面命令!0条件为真，才会执行{print $1}，反之同样道理0或条件为假则不会执行{action}

> - (3)/pat1/,/pat2/ 不支持直接给出数字
> &emsp;&emsp;awk -F: '/^root\>/,/^nobody\>/{print $1}'/etc/passwd
> &emsp;&emsp;awk -F: '(NR>=10&&NR<=20){print NR,$1}'/etc/passwd

``` 

[root@Centos6 ~]#awk -F: '/^root\>/,/^halt\>/{printf "%-10s %d\n", $1,$3}' /etc/passwd

root       0
bin        1
daemon     2
adm        3
lp         4
sync       5
shutdown   6
halt       7

```
> 我们看到awk配合printf将用户名称从root到halt的行及对应的ID号打印出来，并且在输出时比较方便，美观

> - (4)BEGIN/END:执行前运行一次或结束前运行一次
> &emsp;&emsp;BEGIN：让用户指定在第一条输入记录被处理之前所发生的动作，通常可在这里设置全局变量。
> &emsp;&emsp;END：让用户在最后一条输入记录被读取之后发生的动作。
> awk -F : 'BEGIN {print "user         id"}{printf "%-10s %d\n", $1,$3}END{print "end"}' /etc/passwd

```

[root@Centos6 ~]#awk -F : 'BEGIN {print "user       id"}{printf "%-10s %d\n", $1,$3}END{print "end"}' /etc/passwd

user       id
root       0
bin        1
daemon     2
adm        3
...
部分省略
...
tcpdump    72
ymd        500
end

```
>
> 这里就不在过多说明

> - (5)空模式: 匹配每一行



## 2 awk中的变量

> awk也是一门语言，用户不仅可以通过选项在awk中自定义变量，其自身包含了许多内置有特定含义的变量，用在awk语句中可以方便我们进行使用
> 下面对其内置变量进行列举，并对其进行解释 

> 变量如下：

#### 2.1 awk内置变量

> $# 当前记录的第#个字段，比如#为1表示第一个字段，#为2表示第二个字段
> $0 表示变量当前行的所有文本内容。

> - FS :输入字段分隔符，默认为空白字符

```

[root@Centos6 ~]#awk -v FS=':' '{print  $1,FS,$3}' /etc/passwd
root : 0
bin : 1
...
部分省略
...
nobody : 99
dbus : 81

```
> 在定义输入字符段时，-v FS=':' 等价于 -F: 

> - OFS:输出字段分隔符，默认为空白字符

```

[root@Centos6 ~]#awk -F:  -v OFS=':' '{print $1,$3}' /etc/passwd

root:0
bin:1
daemon:2
...
部分省略
...
rpc:32
rtkit:499

```

> - RS:输入文本信息换行符，原换行符仍有效

```

# 对输入的文本以空格作为一个行，进行打印
[root@Centos6 ~]#awk -v RS=' ' '{print }' /etc/issue

CentOS
release
6.9
(Final)
Kernel
\r
on
an
\m

```

> - ORS:输出文本信息换行符，用指定符号代替换行符

```

# 对输入的文本以空格作为一个行，输出文本的换行符用#号代替
[root@Centos6 ~]#awk -v RS=' ' -v ORS='###' '{print }' /etc/issue

CentOS###release###6.9###(Final)
Kernel###\r###on###an###\m

```
> - NF:字段数量

```

#每个操作行中的字段数量
[root@Centos6 ~]#awk '{print NF}' /etc/issue

4
5

```
> - NR:行号

```

# 显示出每个操作行所对应的行号数
[root@Centos6 ~]#awk '{print NR}' /etc/passwd

1
2
3
4
5
6
7
以下省略
...

```
> - FNR:对输入的多个文件分别计行号数

```

# 对输入的/etc/issue  /etc/passwd文件分别计行号数
[root@Centos6 ~]#awk '{print FNR}' /etc/issue  /etc/passwd

1
2
1
2
3
4
5
以下省略
...

```
> - FILENAME:当前文件名

```

[root@Centos6 ~]#awk '{print FILENAME}' /etc/issue 

/etc/issue
/etc/issue

```
> 文件共有两行，awk默认对所有的行进行处理，才会出现每行都显示一次当前的文件名

> - ARGC:命令行参数的个数

```

[root@Centos6 ~]#awk '{print ARGC}' /etc/issue

2
2

```
> 显示同上

> - ARGV:数组，保存命令行所给定的各参数,只有ARGV[0],ARGV[1]两个

```

[root@Centos6 ~]#awk 'BEGIN{print ARGV[0],ARGV[1]}' /etc/fstab  /etc/issue

awk /etc/fstab

```
> 这个命令中，ARGV[0]保存awk，ARGV[1]保存/etc/fstab

#### 2.2 awk自定义变量

> 除了awk自带的内置变量，使用者还可以自己定义变量

**自定义变量(区分字符大小写)**

> &emsp;&emsp;(1) -v var=value
> &emsp;&emsp;(2) 在program中直接定义

```

awk -v test='hello user' 'BEGIN{print test}'
awk 'BEGIN{test="hello,user";print test}'

[root@Centos6 ~]#awk -v test='hello user' 'BEGIN{print test}'
hello user

```

## 4 awk操作符

> awk里面的所支持的操作符，与shall当中的操作符大同小异，这里不过的介绍，只列举出来常见的操作符，及简单的示例

 操作符| 描述
 :---: | :---:  
x+y, x-y, x*y, x/y, x^y, x%y,-x:转换为负数,+x: 转换为数值| 算数操作符
=, +=, -=, *=, /=, %=, ^=,++, --| 赋值操作符
==, !=, >, >=, <, <=| 比较操作符
~ , !~| 模式匹配符
&&,\|\|| 逻辑操作符
function_name (para1,para2)| 函数表达式
? :| 条件表达式
in| 数组成员

```

# 模式匹配后执行action
[root@Centos6 ~]#awk -F: '$0 ~ /root/{print $1,$3}' /etc/passwd

root 0
operator 11

# 匹配UID为0的行，将其打印出来
[root@Centos6 ~]#awk -F: '$3==0' /etc/passwd

root:x:0:0:root:/root:/bin/bash

# 打印UID>=0,并且UID<=500的用户名及ID号
[root@Centos6 ~]#awk -F: '$3>=0 && $3<=500 {printf "%-10s %d\n",$1,$3}' /etc/passwd

root       0
bin        1
daemon     2
adm        3
...
部分省略
...
tcpdump    72
ymd        500

```


## 5 awk的数据处理
---

> awk的输出操作就是之前例子当中的{print ...}，接下来我们详细的对其进行介绍

> 下面看一下awk的输出的主要部分

> - 输出命令
> - 数组
> - 内置函数
> - 控制语句

> 接下来进行详细的解释

### (1)输出命令

> awk中提供了两种输出print和printf

#### print的使用格式：
---

> 使用格式：
```
	print item1, item2, ...
```
**注意：**
> - 1、各项目之间使用逗号隔开，而输出时则以空白字符分隔
> - 2、输出的item可以为字符串或数值、当前记录的字段(如$1)、变量或awk的表达式；数值会先转换为字符串，而后再输出
> - 3、print命令后面的item可以省略，此时其功能相当于print $0, 因此，如果想输出空白行，则需要使用print ""

```
[root@Centos6 ~]#awk 'BEGIN { print "1\n2\n3" }'

1
2
3

[root@Centos6 ~]#awk -F: '{ print $1, $3 }' /etc/passwd

root 0
bin 1
daemon 2
adm 3
部分省略
...

```

**这里需要说明一下: awk  -F '[[:space:]:]' {action}    awk中可以指定多个分隔符来进行操作，在有时候使用比较方便**

#### printf的使用格式：
---
> 使用格式：(与C语言当中的printf命令基本相同)

```
	printf format, item1, item2, ...
```

**注意：**
> - 1、其与print命令的最大不同是，printf需要指定(格式)format；
> - 2、format用于指定后面的每个item的输出格式；
> - 3、printf语句不会自动打印换行符；\n

> **format格式的指示符都以%开头，后跟一个字符：**
> - 格式如下

```

	%c: 显示字符的ASCII码
	%d, %i：十进制整数
	%e, %E：科学计数法显示数值
	%f: 显示浮点数
	%g, %G: 以科学计数法的格式或浮点数的格式显示数值
	%s: 显示字符串
	%u: 无符号整数
	%%: 显示%自身
修饰符：
	N: 显示宽度；
	-: 左对齐；
	+：显示数值符号
	
```

> 其中%d、%f、%s较为常用，当处理一些文本格式比较复杂时候printf，更加简洁，美观

> 上文列举出的例子

``` 

[root@Centos6 ~]#awk -F: '{printf "%-15s %i\n",$1,$3}' /etc/passwd
root            0
bin             1
daemon          2
adm             3
...
部分省略
...
gdm             42
pulse           497
sshd            74
tcpdump         72
ymd             500

```
> 看起来很是舒服！


### (2)数组

> 在awk内部，只支持关联数组

> 其格式为：

```
	array[index-expression]
```

**注意: **
> &emsp;&emsp; index-expression
> - (1) 因其是关联数组，可以使用任意字符串表示，但要注意不要设定成，awk的内置变量
> - (2) 如果数组元素事先不存在，那么在引用其时，awk会自动创建此元素并初始化为空串
	
> 因此，要判断某数据组中是否存在某元素，需要使用index in array的方式
> 要遍历数组中的每一个元素，需要使用如下的特殊结构：
> &emsp;&emsp;	for (var in array) { statement1, ... }
**其中，var用于引用数组下标，而不是元素值**

```

# 定义数组并赋值输出
[root@Centos6 ~]#awk 'BEGIN{test[1]="one";test[2]="two";print test[1]}'
one

# 遍历数组，并对出现相同匹配值计数，如下例子为统计日志中IP的访问量
[root@Centos6 ~]# awk '{test[$1]++}END{for(i in test){printf "%-18s %-d\n", i,test[i]}}' ./access_log

172.18.253.3       59
172.18.250.59      447
172.18.34.199      162
172.18.50.99       10
172.18.252.197     377
172.18.250.194     3
172.18.99.99       11
172.18.11.1        195
172.18.251.196     363
172.18.251.112     1044
以下省略
...

test[$1]++  : 以每个记录的字段为标示，并对出现相同的记录计数

for(i in test ): 对遍历数组中的所有元素，并将数组中的标示赋值给i

```
#### 删除定义的数组

> &emsp;&emsp; delete array[index] 



### (3)内置函数

> awk也是较高大上了，当然会用到函数了，下面我们来看一下

> #### 内置数学函数:

>  数学函数如下表: 

函数名称| 返回值
:---:| :---:
rand()| 0-1之间的数
sin(x)| 正弦函数
cos(x)| 余弦函数
atan2(x,y)| 余切函数
int(x)| 取整
sqrt(x)| 平方根

**示例**

```

随机生成5个数乘以100，并对其结果取整
[root@Centos6 ~]# awk 'BEGIN{rand(); for (i=1;i<=5;i++)print int(rand()*100) }'

29
84
15
58
19

```

> #### 字符串函数

> - 1、length("s"): 指定返回字符串的长度

> - 2、sub(r,s,"t"):对t字符串进行搜索，r为匹配的内容，并将第一个匹配到的r用s替换掉

```

# sub替换第一次匹配的内容
[root@Centos6 ~]#date "+%F"|awk 'sub(/-/,":",$0)'
2017:09-05
# gsub为贪婪模式，替换所有匹配到的内容
[root@Centos6 ~]#date "+%F"|awk 'gsub(/-/,":",$0)'
2017:09:05

```

> -3、split(s,array,"r"):以r为分隔符，对s字符串进行切割，并将切割好的字段结果保存到指定的array的数组中，第一个为array[1],第二个为[2],以此类推

```

[root@Centos6 ~]#date "+%F"|awk 'split($0,test,"-");END{print test[0],test[1],test[2],test[3]}'

2017-09-05
2017 09 05

```

> #### 自定义函数

> 自定义函数格式：

```

function name ( parameter, parameter, ... ) {
		statements
return expression
}

```
> 自定义函数，可以指定输入的参数个数，以及返回的参数表达式

**示例：**

```

#定义一个比较大小的函数 
function max(v1,v2) {
	v1>v2?var=v1:var=v2
	return var
}

BEGIN{a=3;b=2;print max(a,b)}

```


###  (4)、控制语句：
----

> ####  if-else
		
> &emsp;&emsp; 格式：if (condition) {then-body} else {[ else-body ]}

**示例：**
```

[root@Centos6 ~]#awk -F: '{if ($3<500) {printf "%-15s  %-s\n", $1, "Admin"} else {printf "%-15s %-s\n", $1, "Common User"}}' /etc/passwd
root             Admin
bin              Admin
daemon           Admin
adm              Admin
lp               Admin
...
部分省略
...
ymd             Common User
部分省略
...

```

**注意**

> 单条语句{}花括号可不加，但';'分号要加

> ####  while

> &emsp;&emsp; 格式:  while (condition){statement1; statment2; ...}

```

[root@Centos6 ~]#awk -F: '{i=1;while (i<=NF) { if (length($i)<=5) {print $i}; i++ }}' /etc/passwd
root
x
0
0
root
/root
bin
以下省略
...		
		
```

> ####  do-while

> &emsp;&emsp; 格式： do {statement1, statement2, ...} while (condition)

```

# 计算1-100的和	
[root@Centos6 ~]#awk 'BEGIN{i=1;do{sum+=i;i++}while(i<=100){print sum}}' 

5050		
		
```
> ####  for

> &emsp;&emsp; 格式： for ( variable assignment; condition; iteration process) { statement1, statement2, ...}
		
```

# 1-100的和
[root@Centos6 ~]#awk 'BEGIN{for(i=0;i<=100;i++)sum+=i;print sum}'
5050

```
> **for循环还可以用来遍历数组元素：**

> &emsp;&emsp; 格式： for (i in array) {statement1, statement2, ...}

```
# 统计/etc/passwd文件中的bash类型
[root@Centos6 ~]#awk -F: '$NF!~/^$/{BASH[$NF]++}END{for(A in BASH){printf "%15s:%i\n",A,BASH[A]}}' /etc/passwd
 /sbin/shutdown:1
      /bin/bash:3
  /sbin/nologin:29
     /sbin/halt:1
      /bin/sync:1

```		
> ####  case

> &emsp;&emsp; 格式： switch (expression) { case VALUE or /REGEXP/: statement1, statement2,... default: statement1, ...}

> ####  break 和 continue
> &emsp;&emsp;	常用于循环或case语句中，含义及用法与shell中的相同，这里不再赘述

> ####  next

> 提前结束```对本行文本```的处理，并接着处理下一行；
> 例如，下面的命令将显示其ID号为奇数的用户：

```

# 统计ID为奇数的用户
[root@Centos6 ~]#awk -F: '{if($3%2==0) next;printf "%-10s %d\n", $1,$3}' /etc/passwd

bin        1
adm        3
sync       5
halt       7
operator   11
gopher     13
nobody     99
dbus       81
usbmuxd    113
rtkit      499
vcsa       69
abrt       173
rpcuser    29
postfix    89
mysql      27
pulse      497

```

> #### **&emsp;&emsp;以上就是awk的全部内容，由于本片文章是基于部分操作写成的，难免有不足及错误的地方，敬请谅解！**




