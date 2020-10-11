---
title: "AWK_Getting"
date: "2020-02-19"
draft: false
toc: true
images:
tags: 
  - AWK
  - Getting
---

## AWK 脚本的结构

```shell
awk ' BEGIN{ print "start" } pattern { commands } END{ print "end" } file
```

awk 脚本通常由3部分组成。 BEGIN ， END 和带模式匹配选项的常见语句块。这3个部分都是可 选项，在脚本中可省略任意部分。

## AWK 脚本的执行

### 一、在命令行中执行 AWK 脚本

AWK 脚本包含在单引号之间

```shell
awk 'BEGIN { statements } { statements } END { end statements }'
```

### 二、把 AWK 脚本写入文件

文件的内容格式如下，在命令使用`awk -f <programfile> <inputfile>`

- `programfile`: AWK 脚本文件
- `inputfile`: 被处理的文件

```shell
BEGIN { statements } { statements } END { end statements }
```

---

## AWK 的工作方式

### 一、AWK工作流程图

![AWK工作流程图](http://upload-images.jianshu.io/upload_images/2640591-55b18ef56ee6342d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**Read**

AWK从输入流（文件，管道或者标准输入）中读取一行，然后存储到内存中。

**Execute**

所有的AWK命令都依次在输入上执行。默认情况下，AWK会对每一行执行命令，我们可以通过提供模式限制这种行为。

**Repeat**

处理过程不断重复，直到到达文件结尾。

### 二、AWK 执行流程

1. 执行 BEGIN { commands } 语句块中的语句。
2. 从文件或 stdin 中读取一行，然后执行 pattern { commands } 。重复这个过程，直到文件全部被读取完毕。
3. 当读至输入流末尾时，执行 END { commands } 语句块。

### 三、要点理解

- BEGIN 语句块在 awk 开始从输入流中读取行之前被执行。这是一个可选的语句块，诸如变量 初始化、打印输出表格的表头等语句通常都可以写入 BEGIN 语句块中。
- END 语句块和 BEGIN 语句块类似。 END 语句块在 awk 从输入流中读取完所有的行之后即被执 行。像打印所有行的分析结果这类汇总信息，都是在 END 语句块中实现的常见任务（例如，在比 较过所有的行之后，打印出最大数）。它也是一个可选的语句块。
- 最重要的部分就是 pattern 语句块中的通用命令。这个语句块同样是可选的。如果不提供该 语句块，则默认执行 { print } ，即打印所读取到的每一行。 awk 对于每一行，都会执行这个语 句块。这就像一个用来读取行的 while 循环，在循环体中提供了相应的语句。
- 每读取一行， awk 就会检查该行和提供的 pattern  是否匹配。pattern 本身可以是正则表达式、条件 语句以及行匹配范围等。如果当前行匹配该 pattern ，则执行 { } 中的语句。
- pattern 是可选的。如果没有提供样式，那么 awk 就认为所有的行都是匹配的，并执行 { } 中的 语句

---

## AWK 的一些特殊变量

|变量名|含义|
|:--:|:--|
|`NR`| 表示记录数量，在执行过程中对应于当前行号。|
|`NF`| 表示字段数量，在执行过程中对应于当前行的字段数。|
|`$0`| 这个变量包含执行过程中当前行的文本内容。|
|`$1`| 这个变量包含第一个字段的文本内容。|
|`$2`| 这个变量包含第二个字段的文本内容。|

|范例|效果|
|:--:|:--|
|`$ awk '{ print $3,$2 }' file`|打印每一行的第2和第3个字段|
|`$ awk 'END{ print NR }' file`|统计文件中的行数|

## 将外部变量值传递给 AWK

借助选项 `-v` ，我们可以将外部值（并非来自 stdin ）传递给 awk 

```shell
$ VAR=10000
$ echo | awk -v VARIABLE=$VAR '{ print VARIABLE }'
10000
```

## 用 getline 读取特定行:

awk 通常默认读取一个文件的所有行。如果只想读取某一行，可以使用 getline 函数。有时候，我们需要从 BEGIN 语句块中读取第一行。

语法： `getline var`
变量 var 就包含了特定行的内容。如果调用不带参数的 getline ，我们可以用 `$0` 、 `$1` 和 `$2` 访问文本行的内容。

```shell
$ seq 5 | awk 'BEGIN { getline; print "Read ahead first line", $0 } { print $0 }'
Read ahead first line 1
2
3
4
5
```

## 使用过滤模式对 AWK 处理的行进行过滤:

|范例|效果|
|:--:|:--|
|`awk 'NR < 5'`|行号小于5 的行|
|`awk 'NR==1,NR==4'`|行号在1到5之间的行|
|`awk '/linux/'`|包含样式linux 的行（可以用正则表达式来指定模式）|
|`awk '!/linux/'`|不包含包含模式为linux|

## 设置字段定界符:

默认的字段定界符是空格。我们也可以用 -F "delimiter" 明确指定一个定界符
`$ awk -F: '{ print $NF }' /etc/passwd`
在 BEGIN 语句块中则可以用 `OFS="delimiter"` 设置输出字段的定界符。

## 从 AWK 中读取命令输出:

将命令的输出结果读入变量 output 的语法如下
`"command" | getline output ;`

```shell
$ echo | awk '{ "grep root /etc/passwd" | getline cmdout ; print cmdout }'
root:x:0:0:root:/root:/bin/bash
```

通过使用 getline ，我们将外部shell命令的输出读入变量 cmdout 。

## 在 awk 中可以使用 for 循环，其格式如下：

`for(i=0; i<10; i++) { print $i ; }`
或者
`for(i in array) { print array[i]; }`

## AWK 内建的字符串控制函数

|范例|效果|
|:--:|:--|
|`length(string)`|返回字符串的长度。|
|`index(string, search_string)`|返回 search_string 在 string 中出现的位置。|
|`split(string, array, delimiter)` |用定界符生成一个字符串列表，并将该列表存 入数组。|
|`substr(string, start-position, end-position)` |在字符串中用字符起止偏移 量生成子串，并返回该子串。|
|`sub(regex, replacement_str, string)` |将正则表达式匹配到的第一处内容替换成 replacment_str 。|
|`gsub(regex, replacment_str, string)` |和 sub() 类似。不过该函数会替换正则表达式匹配到的所有内容。|
|`match(regex, string)` |检查正则表达式是否能够匹配字符串。如果能够匹配，返回 非0值；否则，返回0。|