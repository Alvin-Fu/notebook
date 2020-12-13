[原文地址](https://docs.google.com/document/d/1lyPIbmsYbXnpNj57a261hgOYVpNRcgydurVQIyZOz_o/pub)
funcdata 函数数据
pseudo-instruction 伪指令
# go1.2运行时符号信息
## 摘要
在go1.2中将支持新的运行时符号。这个是内部的改变，不对用户可见的，但是他们删除了一些不稳定的代码，减少内存占用，使得gc的实现更加的简单。
## 背景
几年前，我为了获得准确的崩溃时的堆栈跟踪，我将plan9的符号和pcln表移动到运行时映射的内存中，并且将代码放到运行时的包中进行分析他们。除了栈追踪以外，栈的遍历功能现在被用于分析和垃圾回收。相对于之前是一个重大的改进，但是也是有一个严重的缺点：go程序首先要做的是花费时间和分配内存将那些表解码用于支持在栈遍历期间的各种格式的查询。在运行时有一条注释(on struct Func)说，最终这个符号表示应该接近这种格式。这样就可以直接读取表格，消除了启动和解码的内存成本。
这个方法还有其他问题：尴尬的在于从编译器到运行时获得新的元数据，因为他是以一种格式被发送到连接器，连接器在符号表中以第二种形式对其进行编码和运行时必须解码新的符号表扩展。Carl是有在添加垃圾回收的时候遇见他。Keith在几个月之前指出那是很混乱的。
## PC-Value表
在讨论我们的计划的细节之前，我们需要去引入pc-value表的概念，他是当前pcln表的泛化。pv-value表将程序计数器表示的代码位置映射到一个int32上。这个特殊的编码不是特别的重要，除了它可以被做的相当紧凑和易于在运行时解码。附件给出了一个示例。
pc-value表是有很多有趣的用法。最明显的是映射程序计数器到行号，但另一个用途就是生成一个pcsp表用于记录每一个程序计数器的堆栈帧的大小，因此堆栈展开总是可以确定当前帧在哪里结束，下一个帧在哪里开始。(事实上，有一些早期的Plan9连接器会生成这个表，但是在5I，6I和8I中是绝没有的。)另一个用途是编译器在每一个调用站点上记录栈变量的活性信息，使得gc可以忽略死栈槽。另一个用途就是让编译器记录哪一些寄存器中的值是需要被刷新到内存中，这样panic就可以适当的更新局部或全局变量。
## 提出的计划
该计划是用内存中的表单将当前的符号系统和pcln表替代，明确的设计被使用与go的运行时而不是任何的预处理。新的伪指令将使得定义每一个自定义函数的元数据和pc-vlue表，连接器是不用改变的。仅仅是编译器和运行时是需要被改变增加新的数据，并且这两者是不可避免的(编译器必须生成这些数据，运行时必须消费这些数据)。
具体来说，这个新函数符号表是一个程序计数器的查询表的形式：
`N pc0 func0 pc1 func1 pc2 func2 ... pc(N-1) func(N-1) pcN`
这个表是一个N的计数，后面是一个交替的pc，函数的元数据指针值得列表。去找到一个给定程序计数器的函数，运行时对pc值进行二分搜索。最终pcN的值就是func(N-1)后面的地址，因此二分查找可以区分出来一个func(N-1)内部的pc和文本段外部的pc。
每一个funcN值是一个从函数符号表开始到下面这个结构的指针偏移量：
```C
 struct        Func

        {

                uintptr        entry;  // start pc

                int32 name;         // name (offset to C string)

                int32 args;         // size of arguments passed to function

                int32 frame;        // size of function frame, including saved caller PC

                int32        pcsp;                // pcsp table (offset to pcvalue table)

                int32        pcfile;          // pcfile table (offset to pcvalue table)

                int32        pcln;                  // pcln table (offset to pcvalue table)

                int32        nfuncdata;          // number of entries in funcdata list

                int32        npcdata;          // number of entries in pcdata list

        };
```
固定的每个函数的原函数保存了函数条目的PC和指向名字和通过pc-value表的给出当前堆栈指针的偏移量、文件编号、行号(已从pclntab开始的偏移量形式)。要查找该文件：给定程序计数器的行，使用pcfile和pcln表计算出文件编号和行号，使用文件编号索引到所有程序的源文件的表中(更多的细节在下面)，并直接使用这个行号。
而不用纠缠于细节，让我们说这个这个是比文件加行号的派生要简单的多。
除了这个固定的每个函数的元数据，还有一个不透明的每个函数元数据指针表和一个不透明pc-vlaue表。这个伪指令` FUNCDATA $2, $sym(SB)`声明funcdata列表的索引2应该是指向"sym"。索引的唯一目的是为了允许定义多种funcdata不发生冲突。这些将需要有一个索引的中心注册表，可能被保存在src/pkg/runtime/funcdata.h。
相似的这个伪指令声明索引3与程序计数器相关联的值是45` PCDATA $3, $45`。每一个pcdata索引编码到一个单独的pc-value表(PCDATA $1, PCDATA $2, 等等)。就像funcdata，索引允许一个给定的函数定义多种pcdata，并且这些索引也将有一个注册表。
在运行时，运行时可以从一个FUNC中给定程序计数器处检索具有给定索引的funcdata或者给定索引的pcdata。一个pcdata流可以产生一个int32型引用相应的数据检索作为一个funcdata指针。
updata的int32在内存中是紧跟着func结构，给pcdata表提供偏移量；如果nfuncdata>0，在npcdata int32紧跟着可能的int32间隙为了对齐并且nfuncdata uintptrs给出funcdata的值。如果pcsp、pcfile、pcln或者任何的pcdata偏移量是0，那么这个表示错误的并且所有的pc值都取-1。
pcfile pc-value表在云文件名表中生成索引。表的格式如下：
`  N+1 name1 name2 name3 … nameN`
其中N+1是一个int32并且每一个nameX是一个int32偏移量到C字符串。偏移量相对于表的开始。没有name0。
函数符号表，文件名表，函数结构和由偏移量引用的数据的内存都是连续的并且记录为一个单一的符号pclntab。最终地址被记录为epclntab。为了方便查找，大多数文件格式中符号本身都有一个对象文件节。



















