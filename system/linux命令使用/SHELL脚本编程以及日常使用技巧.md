# SHELL脚本编程以及日常使用技巧

---
[TOC]
# 基础
## 从一个命令行开始
``` shell
grep --context=2 -niE 'printf|log' -- *.{h,cpp} > /tmp/result 2>/dev/null
```
该命令行使用`grep`命令在当前目录下的后缀为`.h`以及`.cpp`的文件中，查找包含`printf`或者`log`的行, 并将结果保存到文件`/tmp/result`。包括了命令行的几个典型元素。这里从左到右依次说明: 
- `grep(1)`为需要执行的命令
- `--context=2`这是一个长选项，并带有一个参数`2`。
- `-niE` 这是三个连写的短选项。
- `'printf|log'`这个是`-E`选项的参数，使用单引号包含。
- `--`这两个破折号用于表示选项结束。在`--`后面的内容将作为参数(`例如: grep -- '-i' foo.h`)。
- `*.{h,cpp}` 这是一个文件匹配模式，在经过shell扩展后，作为`grep`命令的参数。
- `> /tmp/result` 其中`>`为文件重定向标志，默认重定向标准输出(0标准输入，1标准输出，2标准错误)，所以这里与`1> /tmp/result`的含意相同: 将标准输出重定向到文件`/tmp/result`。
- `2>/dev/null` 这里将标准错误重定向到`/dev/null`，这个是一个特殊的系统文件，任何输出到这个文件的内容将会被丢弃。所以，这里的意思是丢弃错误信息。另外，从`/dev/null`读入，将会马上返回一个`EOF(end of file)`。

上面的命令行中，需要说明的几点是：
- 命令行的一般格式为`cmd [options] [args]`
- `-`开头的内容称为(短)选项(option)，相应的，`--`开头的内容称为长选项。长短选项的意义是一样的，只是长选项的可读性更好，方便理解使用。
- 选项可以带一个或零个参数。
- 不带参数的选项可以连着写，例如上面的`-niE`
- 选项之后的内容称为参数(agument), 例如上面的`*.{h,cpp}`。
- 文件匹配模式与正则表达式是不同的。
- 命令的输出中，需要将正常输出的内容和错误信息输出分开。
- 单引号引用为强引用，引号内的内容不进行扩展；双引号内的内容用允许部分扩展(变量名扩展以及命令扩展)。

## SHELL命令解析流程
``` flow
st=>start: 将命令分隔成tokens
cond1=>condition: 检查第一个token是否为关键字
cond2=>condition: 检查第一个token
operr=>operation: 语法错误
op1=>operation: 波浪号展开
op2=>operation: 变量替换
op3=>operation: 命令替换
op4=>operation: 算数表达式替换
op5=>operation: 展开的文本进行单词分隔
op6=>operation: 通配符展开
op7=>operation: 命令查找：特殊内建命令、函数、内建命令、可执行文件
op8=>operation: 完成IO重定向以及其他同类型事项后
cond3=>condition: 执行命令
e=>end

st->cond1
cond1(yes)->cond2
cond1(no)->operr
cond2(yes)->op1
op1->op2->op3->op4
op4->op5->op6->op7
op7->op8->cond3
cond3(yes, left)->st

```
注: 执行命令这里，如果执行的命令是`eval`的话，会重新从开始走一遍扫描流程。因为Markdown流程图这里不好画，所以就暂时留空了。具体可以参考[Shell脚本学习指南](https://book.douban.com/subject/3519360/)关于命令解析部分。
这里引用一张网上的图:
![shell命令解析流程](https://peanut7379.github.io/img/bash%E6%B5%81%E7%A8%8B.png)
## 常见的命令
### 文本处理
大部分Linux命令，特别是文本处理命令都是从标准输入读入数据，并输出到标准输出。下面是一些常用且应该熟练掌握的命令。
- grep
- sed
- awk
- sort
- diff
- vi
### 其他常用命令
- crontab
- curl 支持ftp，http等多种协议处理
- find
- ftp
- file 查看文件类型
- free
- lsof  查看打开的文件（文件被哪个进程打开；进程打开了哪些文件）
- jq 处理json
- pgrep
- ps
- top
- tar
- tcpdump
- rsync 文件拷贝同步，支持远程拷贝，使用差量快速同步
- df
- du
- bc 强大的可编程计算器
- md5sum
- hexdump
- usradd
- usrmod
- chown
- chmod
## 学会看手册
命令有很多，而且选项也很多，单靠记忆是很难全部记住的。所以要学会`RTFM`。下面是看手册也的一些基本要点:

- 从`man man`开始。
- 手册页分了7个section。每个section包含了不同范畴的手册页。例如，secction 1是shell命令的手册页，section 3是库函数的手册页。平常我们看到的手册页中标明的下标索引，其实就是手册页的section。例如`ls(1)`以及`printf(3)`，其中的`(1)`和`(3)`指引用户使用命令`man 1 ls`, `man 3 printf`去查看`section 1`或`section 3`的手册页:
``` text
       The standard sections of the manual include:

       1      User Commands

       2      System Calls

       3      C Library Functions

       4      Devices and Special Files

       5      File Formats and Conventions

       6      Games et. Al.

       7      Miscellanea

       8      System Administration tools and Daemons

```
- 手册页通常包含了`标题`,`名称`,`概要`,`描述`,`选项`,`环境变量`,`返回值`,`SEE ALSO`等。这里简单介绍一下`概要`部分:
>pgrep [-flvx] [-d delimiter] [-n|-o] [-P ppid,...] [-g pgrp,...]
            [-s sid,...] [-u euid,...] [-U uid,...] [-G gid,...]
            [-t term,...] *pattern*

    其中`[]`包含的内容均为可选内容，不在`[]`内的内容则为必选内容。可有可无；`|`则表示或；`...`则表示多个。斜体字必须用适当的值替换, 例如上面的*pattern*。
# 脚本初步
## 原则

- 一次做好一件事
- 处理文本，不要处理二进制数据
文本易懂，而且可用的工具更多。
- 使用正则表达式
使用正则，功能会更强大。
- 默认使用标准输入/输出
为了和其他工具在管道中使用
- 避免喋喋不休
仅输出必要的信息。unix工具程序遵循你叫它做什么，它就做什么的设计哲学。它们不会问“Are you sure?”
- 输出格式必须与可接受的输入格式一致
保持输入格式一致，以便其他工具可以继续处理
- 让工具去做困难的部分
尽量使用已有成熟的工具
- 构建特定工具前，先想一想
是否有必要，其他人是否也要做，是否这个特殊的要求是一个一般的要求的特例？

## 编码规范
可以参考[google shell脚本编程规范](https://zh-google-styleguide.readthedocs.io/en/latest/google-shell-styleguide/contents/)
这里特别提两点，变量名使用小写加小划线，环境变量以及全局变量放开头，并用大写加下划线；
shell脚本不需要用`.sh`作为结尾，除非是作为库函数使用，而且作为库函数使用时，不需要有执行权限。之所以不需要以`.sh`结尾的原因在于，文件后缀对于Linux下的可执行文件没有特殊意义，另外，脚本有可能使用其他语言重写，调用方只需要知道脚本的功能，没有必要知道脚本的编写语言。
## 脚本的执行
Linux在执行命令的时候，首先会判断命令是否是一个可执行的二进制文件，如果是，则直接执行。如果不是且文件以`#!`开头，则把该命令作为一个脚本执行（解释器文件）。并以`#!`后面指定的命令作为解释器。流程如下：

``` flow
st=>start: 执行命令foo arg1
cond=>condition: 是否为可执行二进制
op1=>operation: 执行二进制命令（foo arg1）
cond2=>condition: 是否为解释器文件
op2=>operation: 执行解释器文件（/path/to/cmd -pf /path/to/foo arg1）
op3=>operation: 报错
e=>end: 结束

st->cond
cond(yes)->op1
op1->e
cond(no, right)->cond2
cond2(yes)->op2->e
cond2(no, right)->op3->e
```
这里假设脚本文件路径为`/path/to/foo`,脚本内容为:
``` shell
#!/path/to/cmd -pf
script content comes here...
```
## 变量
bash支持定义readonly变量，整型变量等。
## 条件判断
bash支持if条件判断(条件测试包括文件是否存在，命令是否执行成功等, 具体可以参考`test(1)`)以及switch case条件判断。(`help case`)
## 循环
bash支持for以及while循环语句。具体可以参考`help for`以及`help while`。
## 主要shell编程参考
以上只是简单介绍，shell脚本编程完整的信息可以参考`bash(1)`手册页以及 [Shell脚本学习指南](https://book.douban.com/subject/3519360/) 

# 日常使用
下面是我个人针对平常日常操作做的一些总结，比较主观，大家可以参考一下。以下说明如无特殊说明，均针对`bash`。
## 一些不好的习惯
- 可执行脚本以`.sh`后缀结尾
- 脚本第一行不是`#!/bin/bash`
- 喜欢使用root用户或其他具有超级用户权限的用户操作
  这样做的坏处首先是不安全，跳过了系统按用户以及权限划分的边界，很容易误操作。打乱了系统的管理。同时，使用超级用户进行日常操作，就像一个野蛮人一样，侵犯了正常操作的普通用户以及造成不便。在公共服务器上，使用超级用户权限时，需要尊重其他人。
- 给文件或目录加上777权限
  没有必要，而且不符合最小权限的原则。
- 习惯一问一答的操作，例如rm -i以及mv -i等
  这样一问一答的操作浪费时间，特别是这种日常操作更是如此。另外，这里有一个陷阱，以`rm -i`为例子，确认式删除好像看起来操作更加谨慎，但实际上可能在习惯了这种操作之后，还是会顺手输入确认，导致误删。从这个角度而言，直接删除和交互式删除是一样。但是交互式删除浪费了更多的时间，而且，另一方面，时刻提醒自己，一旦执行`rm`就会删除，会让你在删除时更加谨慎，并养成习惯。真正谨慎的操作是养成谨慎的习惯。
- 脚本不检查输入
  严格检查外部输入，内部错误则要快速出错。
- 文件或目录名称滥用大写字母
  一般使用大写字母的作用是为了强调，但是如果滥用大写字母，就起不到强调的作用了！就像到处使用叹号一样！！而且输入的时候，要多按一个shift键！！！
## 一些好的习惯
- 保证脚本在任何工作目录都可以正常执行，关键是在脚本里使用绝对路径。
- 合理使用引号。例如，`grep`的*pattern*参数最好用单引号包起来。
- 谨慎处理批量操作，尽量精确匹配。特别是删除文件等。
- 文件或目录名不要包含对shell有特殊含义的特殊字符，例如`()`,`>`,`|`等。
- 从标准输入读入数据，输出到标准输出，同时错误信息输出到标准错误
## 一些容易混淆的概念
### 正则与文件匹配模式
正则与平常在shell命令行中使用的文件匹配模式是两种不同的东西。正则表达式有多种实现，日常日用最多的是`BRE(basic RE)`和`ERE(extend RE)`，除此之外还有功能强大的`PRE(Perl RE)`。文本处理命令一般都使用正则。而文件匹配模式一般仅在在shell命令行解析中使用。(`case`中使用的也是文件匹配模式)。具体可以参考手册页`man 7 glob`。这里举一个例子:
``` shell
grep -E 'log_(debug|info)\(' *.{h,cpp}
grep '[a-z]*' [e-f]*.h
```
`log_(debug|info)\(`使用的`ERE`的语法，支持成组以及或等高级功能，这里匹配`log_debug(`或者`log_info(`；`[a-z]*`使用的是`BRE`,支持一般的正则匹配, 这里匹配任意个小写字母(注意，0个也是任意个)。`*.{h,cpp}`以及`[e-f]*.h`则为文件匹配模式，分别匹配当前目录下以`.h`和`.cpp`结尾的文件，以及`e`或`f`开头并加上任意字符以及以`.h`结尾的文件。
### 环境变量
设置环境变量仅对当前进程以及它的子进程有效。
## 一些常用的技巧
以下罗列的一些技巧在参考的书籍中基本都有提及。如果本身对shell编程了解不多并且有时间的话，系统的查阅书籍。这样可以花比较少的时间，系统以及全面掌握。
### Bash快捷键
在输入命令行的时候，`bash`支持`vi`以及`emacs`两个模式。一般`emacs`模式更方便。这里罗列一下常用的快捷键, 完整版快捷键说明可以参考[readline-emacs-editing-mode-cheat-sheet.pdf](https://github.com/pkrumins/bash-emacs-editing-mode-cheat-sheet/blob/master/readline-emacs-editing-mode-cheat-sheet.pdf)或者使用`bash`内置命令`bind -p`查看：
``` text
======================== Keyboard Shortcut Summary ========================

.--------------.-------------------.----------------------------------------.
|              |                   |                                        |
| Shortcut     | Function          | Description                            |
|              |                   |                                        |
'--------------'-------------------'----------------------------------------'
| Commands for Moving:                                                      |
'--------------.-------------------.----------------------------------------'
| C-a          | beginning-of-line | Move to the beginning of line.         |
'--------------+-------------------+----------------------------------------'
| C-e          | end-of-line       | Move to the end of line.               |
'--------------+-------------------+----------------------------------------'
| C-f          | forward-char      | Move forward a character.              |
'--------------+-------------------+----------------------------------------'
| C-b          | backward-char     | Move back a character.                 |
'--------------+-------------------+----------------------------------------'
| M-f          | forward-word      | Move forward a word.                   |
'--------------+-------------------+----------------------------------------'
| M-b          | backward-word     | Move backward a word.                  |
'--------------+-------------------+----------------------------------------'
| C-l          | clear-screen      | Clear the screen leaving the current   |
|              |                   | line at the top of the screen.         |
'--------------'-------------------'----------------------------------------'
| Commands for Changing Text:                                               |
'--------------.-------------------.----------------------------------------'
| Rubout       | backward-delete-  | Delete one character backward.         |
|              | char              |                                        |
'--------------+-------------------+----------------------------------------'
| C-q or C-v   | quoted-insert     | Quoted insert.                         |
'--------------+-------------------+----------------------------------------'
| M-TAB or     | tab-insert        | Insert a tab character.                |
| M-C-i        |                   |                                        |
'--------------+-------------------+----------------------------------------'
| a, b, A, 1,  | self-insert       | Insert the character typed.            |
| ...          |                   |                                        |
'--------------+-------------------+----------------------------------------'
| C-t          | transpose-chars   | Exchange the char before cursor with   |
|              |                   | the character at cursor.               |
'--------------+-------------------+----------------------------------------'
| M-t          | transpose-words   | Exchange the word before cursor with   |
|              |                   | the word at cursor.                    |
'--------------+-------------------+----------------------------------------'
| M-u          | upcase-word       | Uppercase the current word.            |
'--------------+-------------------+----------------------------------------'
| M-l          | downcase-word     | Lowercase the current word.            |
'--------------+-------------------+----------------------------------------'
| M-c          | capitalize-word   | Capitalize the current word.           |
'--------------+-------------------+----------------------------------------'
| (unbound)    | overwrite-mode    | Toggle overwrite mode.                 |
'--------------'-------------------'----------------------------------------'
| Killing and Yanking:                                                      |
'--------------.-------------------.----------------------------------------'
| C-k          | kill-line         | Kill the text from point to the end of |
|              |                   | the line.                              |
'--------------+-------------------+----------------------------------------'
| C-u          | unix-line-discard | Kill backward from point to the        |
|              |                   | beginning of the line.                 |
'--------------+-------------------+----------------------------------------'
```

另外，这个readline快捷键在很多命令输入的地方都适用，比如`MySQL`客户端等。
### Bash配置文件
可以参考`bash(1)`手册页中的`FILES`里的说明，这里罗列一下:
``` text
FILES
       /bin/bash
              The bash executable
       /etc/profile
              The systemwide initialization file, executed for login shells
       /etc/bash.bash_logout
              The systemwide login shell cleanup file, executed when a login shell exits
       ~/.bash_profile
              The personal initialization file, executed for login shells
       ~/.bashrc
              The individual per-interactive-shell startup file
       ~/.bash_logout
              The individual login shell cleanup file, executed when a login shell exits
       ~/.inputrc
              Individual readline initialization file
```
`/etc`目录下的配置文件是系统级的配置文件，有首先加载。用户主目录下的文件则会在用户登录的时候加载。
这里需要注意的是`profile`是`bash`作为登录shell的时候才会执行，而`.bashrc`是交互shell时执行命令。`rc`正是`run command`的缩写。所以，这里需要注意的一点是，用户环境变量的设置一般要放在`~/.bash_profile`, 否则如果放在`~/.bashrc`中时，如果以非交互shell的方式运行`bash`，则会导致用户的环境变量得不到设置。另外，登录shell也就是需要做登录时启动的shell。再另外`su - appuser`与`su appuser`的主要区别也是前者会模拟登录shell，加载`~/.bashr_profile`。

### 命令组合
可以使用`||`以及`&&`来实现简化操作，例如:
``` shell
[ -d /tmp/foo ] && echo '/tmp/foo exist' || mkdir /tmp/foo
```
### 循环
``` shell
# ls -l *.h
for f in *.h
do
ls -l $f
done

# md5sum execute file of process which is end with `-s 200'
for pid in $(pgrep -f -- '-s 200$')
do
md5sum  $(readlink /proc/${pid}/exe)
done | sort
```
可以使用内置命令`for`执行一般的循环操作。
### 文本去重
``` shell
# 去重并保持输入顺序
awk '!a[$0]++'
awk '!a[$0]++ {print}'
awk '!a[$0]++ {print $0}'

# 去重并排序
awk '!a[$0]++' | sort
```

这里前3条命令的效果是一样的。`awk`的默认行为是打印`$0`，也就是打印整行。
### 使用`trap`在脚本内捕捉信号
``` shell
readonly PROGRAM=$(basename $0)
readonly TMP_OUTPUT=/tmp/${PROGRAM}.tmp

trap clean EXIT

function clean()
{
    rm -f $TMP_OUTPUT
}
```
上面的例子在脚本退出的时候，调用自定义函数`clean`执行清理工作，不管脚本是正常退出还是被kill。

### 原子操作
有时需要在脚本里执行原子操作，可以使用`mkdir`的检测目录是否存在和创建目录为原子操作实现。(注意，在NTFS文件系统中为非原子操作)。
``` shell
readonly PROGRAM=$(basename $0)
readonly LOCK_DIR=/tmp/${PROGRAM}.lock
trap unlock EXIT

function unlock ()
{
    rmdir -f ${LOCK_DIR}
}

if mkdir ${LOCK_DIR}; then
    # get lock, do what we want here...
    ...
    unlock
fi
```
### 了解并使用Linux系统的特殊文件
/dev/stdin
:   标准输入。一般也可以`-` 表示标准输入。例如：
``` shell
# grep from stdin
grep 'abc'

# also grep from stdin
grep 'abc' -

# still grep from stdin
grep 'abc' /dev/stdin
```

/dev/stdout
:   标准输出

/dev/stderr
:   标准错误

/dev/null
:   空文件，读入马上返回`EOF`;写入内容则被丢弃。

/dev/zero
:   zero文件，读入返回`0`;写入内容会被丢去。

/dev/random
:   随机数发生器。  

/dev/urandom
:   非阻塞的随机数发生器。

/proc/*pid*/
:   进程信息伪文件系统。可以在里查找到进程的相关信息。具体参考手册`man 5 proc`以及维基百科[procfs](https://zh.wikipedia.org/wiki/Procfs)。

### 把进程放后台的4种方法
``` shell
# run at background
sleep 30 &

# run at background and remove jobs from current shell
sleep 30 & disown

# ignore hang signal
nohup sleep 30

# execute command in sub-shell at backgroud and also make sub-shell run at backgroud
(sleep 10 &)&
```
### 一个好用的目录跳转工具
如果仅使用cd在目录之间跳转，往往需要输入很多字符，效率很低。这里推荐一个简单的shell脚本[z](https://github.com/rupa/z)。
这里只是举例说明一下，有很多好用的辅助工具可以改进命令行操作的体验。

# 参考
[命令行的艺术](https://github.com/jlevy/the-art-of-command-line/blob/master/README-zh.md)
[Unix & Linux大学教程](https://book.douban.com/subject/4253716/)
[Shell脚本学习指南](https://book.douban.com/subject/3519360/)
[UNIX用户手册](https://book.douban.com/subject/1110484/)
[UNIX环境高级编程](https://book.douban.com/subject/1788421/)
