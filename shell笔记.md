---
title: shell笔记
date: 2017-12-25 19:30:27
categories:
- DevOps
---

## 教程来源
[教程来源](http://www.runoob.com/linux/linux-shell.html)
<!--more-->

## 类型

"#!"是一个约定的标记，它告诉系统这个脚本需要什么解释器来执行，即使用哪一种 Shell。

```
#!/bin/bash
#!/bin/sh
```

## Shell 变量

### 定义和使用
```
your_name="qinjx"
echo &#36;your_name
echo ${your_name}

for file in `ls /etc`

for skill in Ada Coffe Action Java; do
    echo "I am good at ${skill}Script"
done
```
### 删除变量
```
unset variable_name
```

### 字符串方法
```
string="abcd"
echo $#36;#string} #输出 4


string="runoob is a great site"
echo $#36;string:1:4} # 输出 unoo

```

### 数组
在Shell中，用括号来表示数组，数组元素用"空格"符号分割开。

```
array_name=(value0 value1 value2 value3)

array_name[0]=value0
array_name[1]=value1
array_name[n]=valuen

valuen=${array_name[n]}

echo ${array_name[@]}
```
使用@符号可以获取数组中的所有元素

## 注释

以"#"开头的行就是注释，会被解释器忽略。
sh里没有多行注释，只能每一行加一个"#"号。

## 传递参数
```
echo "Shell 传递参数实例！";
echo "执行的文件名：&#36;0";
echo "第一个参数为：&#36;1";
echo "第二个参数为：&#36;2";
echo "第三个参数为：&#36;3";


&#36; chmod +x test.sh 
&#36; ./test.sh 1 2 3
Shell 传递参数实例！
执行的文件名：./test.sh
第一个参数为：1
第二个参数为：2
第三个参数为：3

```

|参数处理	|说明|
|---|---:|
|$#	|传递到脚本的参数个数|
|$*	|以一个单字符串显示所有向脚本传递的参数。如"$*"用「"」括起来的情况、以"$1 &#36;2 … &#36;n"的形式输出所有参数。|
|$$	|脚本运行的当前进程ID号|
|$!	|后台运行的最后一个进程的ID号|
|$@	|与$*相同，但是使用时加引号，并在引号中返回每个参数。如"$@"用「"」括起来的情况、以"$1" "$2" … "$n" 的形式输出所有参数。|
|$- |	显示Shell使用的当前选项，与set命令功能相同。|
|$?	|显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。|


## 运算符

### 算术运算符
假定变量 a 为 10，变量 b 为 20：


|运算符|说明|	举例|
|----|----|:-----|
|+	|加法| expr &#36;a + &#36;b 结果为 30。|
|-	|减法|	expr &#36;a - &#36;b 结果为 -10。|
|*	|乘法|	`expr &#36;a \* &#36;b` 结果为  200。|
|/	|除法|	`expr &#36;b / &#36;a` 结果为 2。|
|%	|取余|	`expr &#36;b % &#36;a` 结果为 0。|
|=	|赋值|	a=$b 将把变量 b 的值赋给 a。|
|==	|相等。用于比较两个数字，相同则返回 true。|	[ &#36;a == &#36;b ] 返回 false。|
|!=	|不相等。用于比较两个数字，不相同则返回 true。|	[ &#36;a != &#36;b ] 返回 true。|

### 关系运算符

|运算符	|说明 |举例|
|-----|----|-----:|
|-eq |检测两个数是否相等，相等返回 true。| [ &#36;a -eq &#36;b ] 返回 false。|
|-ne	|检测两个数是否相等，不相等返回 true。|	[ &#36;a -ne &#36;b ] 返回 true。|
|-gt	|检测左边的数是否大于右边的，如果是，则返回 true。|	[ &#36;a -gt &#36;b ] 返回 false。|
|-lt	|检测左边的数是否小于右边的，如果是，则返回 true。|	[ &#36;a -lt &#36;b ] 返回 true。|
|-ge	|检测左边的数是否大于等于右边的，如果是，则返回 true。	|[ &#36;a -ge &#36;b ] 返回 false。|
|-le	|检测左边的数是否小于等于右边的，如果是，则返回 true。	|[ &#36;a -le &#36;b ] 返回 true。|

### 布尔运算符

|运算符	|说明|	举例|
|-----|----|-----|
|!	|非运算，表达式为 true 则返回 false，否则返回 true。|	[ ! false ] 返回 true。|
|-o	|或运算，有一个表达式为 true 则返回 true。	|[ &#36;a -lt 20 -o &#36;b -gt 100 ] 返回 true。|
|-a	|与运算，两个表达式都为 true 才返回 true。	|[ &#36;a -lt 20 -a &#36;b -gt 100 ] 返回 false。|

### 逻辑运算符

|运算符	|说明|	举例|
|-----|----|-----|
|&&	|逻辑的 AND	|[[ &#36;a -lt 100 && &#36;b -gt 100 ]] 返回 false|
| \u007c\u007c	|逻辑的 OR	|[[ &#36;a -lt 100 \u007c\u007c &#36;b -gt 100 ]] 返回 true|


### 字符串运算符

### 文件测试运算符


## echo命令
用于字符串的输出

```shell
echo "It is a test"

echo "\"It is a test\""

#read 命令从标准输入中读取一行,并把输入行的每个字段的值指定给 shell 变量
#!/bin/sh
read name 
echo "$name It is a test"

#显示换行
echo -e "OK! \n" # -e 开启转义
echo "It it a test"

#显示不换行
#!/bin/sh
echo -e "OK! \c" # -e 开启转义 \c 不换行
echo "It is a test"

#显示结果定向至文件
echo "It is a test" > myfile

# 原样输出字符串，不进行转义或取变量(用单引号)
echo '$name\"'

#显示命令执行结果
echo `date`
```

## printf 命令

```
printf  format-string  [arguments...]

&#36; echo "Hello, Shell"
Hello, Shell
&#36; printf "Hello, Shell\n"
Hello, Shell

```


## test 命令

Shell中的 test 命令用于检查某个条件是否成立，它可以进行数值、字符和文件三个方面的测试。

### 数值测试
|参数	|说明|
|----|----:|
|-eq	|等于则为真|
|-ne	|不等于则为真|
|-gt	|大于则为真|
|-ge	|大于等于则为真|
|-lt	|小于则为真|
|-le	|小于等于则为真|

```
num1=100
num2=100
if test $[num1] -eq $[num2]
then
    echo '两个数相等！'
else
    echo '两个数不相等！'
fi


# 代码中的 [] 执行基本的算数运算，如：

#!/bin/bash

a=5
b=6

result=$[a+b] # 注意等号两边不能有空格
echo "result 为： &#36;result"
```

### 字符串测试

|参数|说明|
|----|----:|
|=	|等于则为真|
|!=	|不相等则为真|
|-z 字符串	|字符串的长度为零则为真|
|-n 字符串	|字符串的长度不为零则为真|

```
num1="123"
num2="cvb"
if test &#36;num1 = &#36;num2
then
    echo '两个字符串相等!'
else
    echo '两个字符串不相等!'
fi
```

### 文件测试

|参数	|说明|
|----|----:|
|-e 文件名	|如果文件存在则为真|
|-r 文件名	|如果文件存在且可读则为真|
|-w 文件名	|如果文件存在且可写则为真|
|-x 文件名	|如果文件存在且可执行则为真|
|-s 文件名	|如果文件存在且至少有一个字符则为真|
|-d 文件名	|如果文件存在且为目录则为真|
|-f 文件名	|如果文件存在且为普通文件则为真|
|-c 文件名	|如果文件存在且为字符型特殊文件则为真|
|-b 文件名	|如果文件存在且为块特殊文件则为真|

```
cd /bin
if test -e ./bash
then
    echo '文件已存在!'
else
    echo '文件不存在!'
fi

cd /bin
if test -e ./notFile -o -e ./bash
then
    echo '有一个文件存在!'
else
    echo '两个文件都不存在'
fi

```

## 流程控制
sh的流程控制不可为空,如果else分支没有语句执行，就不要写这个else。
### if else
```
if condition
then
    command1 
    command2
    ...
    commandN 
fi



if condition1
then
    command1
elif condition2 
then 
    command2
else
    commandN
fi
```

### for 循环

```

for var in item1 item2 ... itemN
do
    command1
    command2
    ...
    commandN
done

```

### while 语句

```
while condition
do
    command
done

```

### 无限循环

```
while :
do
    command
done

```

### until 循环
until循环执行一系列命令直至条件为真时停止。
until循环与while循环在处理方式上刚好相反。
一般while循环优于until循环，但在某些时候—也只是极少数情况下，until循环更加有用。
```
until condition
do
    command
done

```

### case

```

case 值 in
模式1)
    command1
    command2
    ...
    commandN
    ;;
模式2）
    command1
    command2
    ...
    commandN
    ;;
esac
```


### 跳出循环
break命令,continue


## Shell 函数

```
[ function ] funname [()]

{

    action;

    [return int;]

}
```

### 函数参数
```
#!/bin/bash
# author:菜鸟教程
# url:www.runoob.com

funWithParam(){
    echo "第一个参数为 &#36;1 !"
    echo "第二个参数为 &#36;2 !"
    echo "第十个参数为 &#36;10 !"
    echo "第十个参数为 ${10} !"
    echo "第十一个参数为 ${11} !"
    echo "参数总数有 $# 个!"
    echo "作为一个字符串输出所有参数 $* !"
}
funWithParam 1 2 3 4 5 6 7 8 9 34 73

```
注意，&#36;10 不能获取第十个参数，获取第十个参数需要${10}。当n>=10时，需要使用${n}来获取参数。
另外，还有几个特殊字符用来处理参数：



|参数处理	|说明|
|----|----:|
|$#	|传递到脚本的参数个数|
|$*	|以一个单字符串显示所有向脚本传递的参数|
|$$	|脚本运行的当前进程ID号|
|$!	|后台运行的最后一个进程的ID号|
|$@	|与$*相同，但是使用时加引号，并在引号中返回每个参数。|
|$-	|显示Shell使用的当前选项，与set命令功能相同。|
|$?	|显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。|


## 输入/输出重定向
大多数 UNIX 系统命令从你的终端接受输入并将所产生的输出发送回​​到您的终端。一个命令通常从一个叫标准输入的地方读取输入，默认情况下，这恰好是你的终端。同样，一个命令通常将其输出写入到标准输出，默认情况下，这也是你的终端。

## 文件包含

Shell 也可以包含外部脚本。这样可以很方便的封装一些公用的代码作为一个独立的文件。
Shell 文件包含的语法格式如下:

```
. filename   # 注意点号(.)和文件名中间有一空格
或
source filename
```







