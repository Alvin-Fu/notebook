## makefile文件格式
### 1、概述
### 2、目标
### 3、前置条件
### 4、命令
命令表示如何更新目标文件，由一行或者多行shell命令组成。
每行命令前必须有一个tab键，想换成其他的可以使用内置变量`.RECIPEPREFIX`
```shell
.RECIPEPREFIX
all:
> echo Hello, world
```
上面的代码指定了>代替tab键。每行命令的开始就是>
**注意**：每行命令在shell中是单独执行的，之间没有继承关系
```shell
var-lost:
	export foo=bar
	echo "foo=[$$foo]"
```
在执行make var-lost时是取不到foo的值的。因为两个命令是在不同的进程中执行的。要向两个命令在同一个shell中执行有三种方法：
- 使用;写在一行 
```shell
var-kept:
    export foo=bar; echo "foo=[$$foo]"
```
- 是\进行转义
```shell
var-kept:
    export foo=bar; \
    echo "foo=[$$foo]"
```
- 加上`.ONESHELL:`命令
```shell
.ONESHELL:
var-kept:
    export foo=bar; 
    echo "foo=[$$foo]"
```
## makefile语法
### 1、回声(echoing)
正常情况使用make时会打印每一条命令，在命令前加@会关闭回声
```shell
test:
    @# 这是测试
    @echo TODO
```
通常是在注释和纯显示的echo命令前加
### 2、通配符
|通配符元素|描述|备注|
|-|-|-|
|*|匹配任意长度的字符串，包括斜杠(/)字符||
|?|匹配任意单字符||
|+|匹配一次或多次出现的前一元素||
|[字符集和]|匹配作为字符集中的一分子的单个字符，字符集合是根据POSIX通配符扩展规则而制定的，例如：[a-z]与a-z范围中的任何ASCII字符相匹配||
|一个字符|精准匹配指定字符||
注意：反斜杠(\)可以禁用他后面字符的特殊意义。需要匹配反斜杠时使用(\\)
### 3、模式匹配
使用%可以将大量同一类型的文件用一条规则进行构建。

### 4、变量和赋值符
四种赋值运算符(=,:=, ?=, +=)
```shell
VARIABLE = value
# 在执行时扩展，允许递归扩展。

VARIABLE := value
# 在定义时扩展。

VARIABLE ?= value
# 只有在该变量为空时才设置值。

VARIABLE += value
# 将值追加到变量的尾端。
```
### 5、内置变量
make命令提供一系列内置变量，比如$(CC)指向当前使用的编译器，$(MAKE)指向当前使用的make工具，主要是为了跨平台的兼容性。
### 6、自动变量
|变量名|功能|
|-|-|
|$(@D)|表示文件的目录部分(不包括斜杠)。如果"$@"表示的是"dir/foo.o"|
|||












