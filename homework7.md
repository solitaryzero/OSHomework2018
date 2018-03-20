# 第七讲视频相关思考题

7.1 了解x86保护模式中的特权级

* X86有几个特权级？
	* 4个

* 不同特权级有什么区别？
	* 特权指令只有在内核态才能执行；
	* 不同特权级使用的数据范围不同；
	* 一些指令在不同特权级下执行内容不同；

* 请说明CPL、DPL和RPL在中断响应、函数调用和指令执行时的作用。
	* CPL：当前进程优先级；
	* DPL：数据段需求的优先级；
	* RPL：进程当前请求的优先级；
	* 访问门时，低优先级进程请求高优先级服务；访问数据时，高优先级访问低优先级数据。

* 写一个示例程序，完成4个特权级间的函数调用和数据访问时特权级控制的作用。
	* 略

7.2 了解特权级切换过程

* 一条指令在执行时会有哪些可能的特权级判断？
	* 执行特权指令是是否在内核态；
	* 访问的数据段是否符合特权级要求；
	* 中断等特殊指令在什么特权级下执行。

* 在什么情况下会出现特权级切换？
	* 系统调用、中断(int,iret)；

* int指令在ring0和ring3的执行行为有什么不同？
	* 在ring3下会额外压栈ss与esp并进行堆栈切换。

* 如何利用int和iret指令完成不同特权级的切换？
	* 在iret前修改堆栈，让其变成对应特权级进入中断时的形式。

* TSS和Task Register的作用是什么？
	* 保存ring0-2的段工作信息、页表信息等内容。

7.3 了解段/页表

* 一条指令执行时最多会出现多少次地址转换？
	* (1+页表级数)次

* 描述X86-32的MMU地址转换过程；
	* 先通过段选择符确定段基址，再通过多级页偏移决定物理帧，最后加上偏移得到实际物理地址。

7.4 了解UCORE建立段/页表

* 分析MMU的使能过程，尽可能详细地分析在执行进入保护械的代码“movl %eax, %cr0 ; ljmp $CODE_SEL, $0x0”时，CPU的状态和寄存器内容的变化。
	* 设置各段基址；
	* 将cr0的保护模式位打开，进入保护模式，开启MMU。

* 分析页表的建立过程；
	* 将一页内存分配为页表；
	* 映射0xc0000000以上地址；
	* 映射0-0x01000000地址；
	* 设置cr3及cr0第31位
	* 取消0-0x01000000地址的映射。
	
# 个人思考题

* x86保护模式中权限管理无处不在，下面哪些时候要检查访问权限() (w4l1)
	* √内存寻址过程中
	* √代码跳转过程中
	* √中断处理过程中
	*  ALU计算过程中

* 请描述ucore OS建立页机制的准备工作包括哪些步骤？ (w4l1)
	* 初始化GDT，完成段机制；
	* 探测物理内存，将空闲物理内存组织成页；
	* 建立页表，设置cr3与cr0寄存器。