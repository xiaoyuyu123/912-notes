Conclusion on Operating System Chpater One
==========================================

## 系统启动流程

### 简单的版本

BIOS -> 加载程序(Bootloader) -> 操作系统

+ BIOS
	- 芯片上电后为实模式，只能索引1M的内存空间。BIOS就在其中，作为ROM的一部分，因此可以成为固件，不会关机就失去数据了。
	- 芯片刚上电时各个寄存器都会有一定的初始值，其中CS:IP就指向BIOS中第一条指令。
	- BIOS中的第一条指令被读入CPU。

+ Bootloader
	- BIOS程序运行中，会将硬盘上的Bootloader读入内存中，然后控制权转向Bootloader。
	- Bootloader加载操作系统到内存中。
	- 控制权转给操作系统，启动完成。

简单的过程就是上面那样，但是这里需要明确一些问题。

> 首先，为什么需要Bootloader这样一个程序，为什么不能直接由BIOS读入操作系统？

+ 这是因为操作系统的种类复杂，有许多不同的操作系统，他们各自具有不同的文件系统。BIOS的内容是出厂固化的，不可能使之可以分辨所有的操作系统。让BIOS的那点程序分辨不同操作系统的文件系统，并将其加载到内存中，会使BIOS的设计非常复杂，以至于BIOS自己就成为了一个操作系统。

+ 而Bootloader并不属于任何的操作系统，不属于任何的磁盘扇区，而是存在于一个叫引导扇区的地方，其中，一个扇区大小512字节，因此Bootloader也是512字节大小。它可以具有独立于操作系统的编码方式，因此可以被BIOS识别。故可以先将Bootloader读入内存，再又Bootloader加载操作系统。

此外，我们看到，BIOS在加载Bootloader的过程中，已经有用到一些操作系统的功能，他们包括

+ 字符显示
+ 磁盘扇区读写
+ 检测内存大小
+ 键盘输入

实际上，这些操作肯定不是操作系统提供的，而是BIOS以中断调用的方式提供的。可以说，BIOS的功能已经类似一个小型的操作系统了。需要注意的是，这些功能只能在实模式下使用，进入保护模式后就不能访问BIOS的这些功能了。这里我们也可以发现，BIOS的另一个重要功能就是切换到保护模式。

### 具体一点的版本

BIOS -> 磁盘主引导扇区的主引导记录 -> 活动分区的引导扇区的引导代码 -> 加载程序Bootloader -> 操作系统

相对于之前的简单版本，多了两个中间流程：读取主引导扇区的引导代码，读取活动分区的引导扇区代码。这两个过程实际上是非常自然的：

+ 现代计算机一般都具有多个分区，因此就可能从不同的硬盘分区读取操作系统代码。此外，还可以选择从即插即用设备启动(U盘)，从网络启动。这使得上面BIOS直接读取Bootloader变得行不通了，因为可能还需要由用户选择启动哪个操作系统，不同的操作系统也需要不同的Bootloader进行加载（因为具有不同的文件系统）。

+ 为什么还需要分区引导扇区，而不是直接由主引导扇区跳转到不同分区的加载程序呢？我的想法是一个分区也可以存在几个操作系统，因此还需要分区引导扇区来加以选择，之后才能跳转到Bootloader。

从上面的分析可以看出，BIOS还需要具有一些其他的功能，像是可以识别到不同分区的操作系统，识别到即插即用设备，从网络启动的话还需要加载网络协议栈。这样的话，系统的启动可以具体为以下的步骤：

+ BIOS
	- 第一条指令被读入内存
	- 进行一些系统初始化工作
		+ 硬件自检
		+ 检测系统中各个关键部件（如内存和显卡）的存在以及工作状态
		+ 查找和执行各个接口卡的BIOS，完成设备的初始化
	- 执行系统BIOS，进行系统检测，如磁盘分区和即插即用设备
	- 读取主引导扇区的主引导记录

+ 主引导记录(MBR, Main Boot Record)
	- 包括三个部分
		+ 启动代码：446字节
			- 检查分区的正确性
			- 加载并跳转到磁盘上的分区引导扇区
		+ 硬盘分区表：64字节 描述每个分区的状态和位置，一个分区占16个字节
		+ 结束符：2字节 0x55AA
	- 执行启动代码，跳转到活动分区的引导程序

+ 活动分区引导扇区
	- 到这里为止，已经出现了文件结构
	- 包含四个部分
		+ 跳转指令： 跳转到启动代码，现在是与平台相关的代码了
		+ 文件卷头： 文件系统描述信息
		+ 启动代码： 跳转到加载程序
		+ 结束标志： 0x55AA

+ 加载程序
	- 读取文件系统中的配置信息
	- 根据配置信息加载操作系统的代码
	- 控制权转交给操作系统

## 中断，异常，系统调用


> 还是一个问题，为什么操作系统需要中断、异常和系统调用？

其实，按照软件工程的思想，这三者都是操作系统对外提供的接口，外部的应用程序可以通过这三种接口进入操作系统内核，而不能直接操作底层的寄存器这种，都是为了操作更加地安全。

这是因为，在计算机运行当中，只有内核才是被信任的第三方，只有内核才能执行特权指令。这就好比你去银行取钱，你可以通过柜台或者是自动取款机取钱，却不可以直接去保险柜里面拿钱。

> 接下来辨析一下中断，异常和系统调用的区别

+ 中断是用于服务硬件的，是处理来自硬件设备的处理请求，如键盘中断这种。
+ 异常是用于处理非法指令或者其他原因导致当前指令执行失败，例如除零异常，缺页异常这种
+ 系统调用是用于应用程序主动地调用操作系统的功能，是应用程序主动发出的服务请求

但是这三者显然具有更多的共同点：

+ 他们都是操作系统对外提供的接口。外部应用只有通过这三种接口才能进入操作系统内核
+ 在实现机制上，三者都是采用中断处理机制。就是说，三者尽管名字不同，处理起来确实类似的
	- 在硬件方面收到了中断处理请求，根据优先级查找中断向量表，找到对应的中断服务例程
	- 这里，中断、异常、系统调用都有各自的中断服务例程
		+ 对于系统调用，一般都是通过INT 80指令进入中断，然后通过中断向量表找到系统调用表，再根据具体的系统调用号找到对应的系统调用程序
		+ 对于异常，会进入对应的异常服务例程。不同的异常自然会有不同的异常服务例程
	- 找到对应的服务例程后，三者都是进入同样的软件方面的机制
	 	- 现场保护
	 	- 中断服务处理
	 	- 清除中断标记
	 	- 现场恢复
+ 此外，他们之间是可以相互嵌套的。例如异常服务例程执行过程中可以会出现硬件中断，异常服务例程执行时也可能会遇到缺页异常。

关于系统调用与常规调用的不同点：

- CALL RET 用于常规调用
- INT IRET 用于系统调用
- 系统调用往往开销更高，这是因为系统调用还涉及到堆栈的切换，从用户堆栈切换到内核堆栈(也是出于安全的考虑)，因此系统调用相对于常规调用，在保护现场的操作中，除了要保存当前的CS:IP以及一些关联寄存器外，还需要保存状态信息(EFLAGS),以及当前用户堆栈的地址(SS和ESP),以便于从内核堆栈恢复到用户堆栈