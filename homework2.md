## 思考题

- 你理解的对于类似ucore这样需要进程/虚存/文件系统的操作系统，在硬件设计上至少需要有哪些直接的支持？至少应该提供哪些功能的特权指令？
    * 需要的硬件支持有CPU，内存，硬盘，I/O设备，时钟，中断。
	* 至少应提供的特权指令有：允许和禁止中断，控制中断禁止屏蔽位；在进程间切换处理；存取用于主存保护的寄存器；执行I/O操作；设置时钟。

- 你理解的x86的实模式和保护模式有什么区别？物理地址、线性地址、逻辑地址的含义分别是什么？
    * 两者能访问的物理内存空间大小不同。实模式只能使用20条地址线，访问1MB的物理内存空间，而保护模式可以使用全部的32条地址线，访问4GB的物理内存空间。
    * 实地址模式不把系统程序和用户程序区别对待，如果用户程序中指针指向了系统程序可能引发严重后果；保护模式中用户程序使用虚拟地址，经系统翻译后才转化为物理地址。
    * 物理地址：系统最终提交给总线用于访问内存或外设的地址。
    * 线性地址：系统段式内存管理下由段基址和段内偏移组成的中间层内存表示。
    * 虚拟地址：用户程序或系统程序在经过地址转化前使用的逻辑地址。
    
- 理解list_entry双向链表数据结构及其4个基本操作函数和ucore中一些基于它的代码实现（此题不用填写内容）

- 对于如下的代码段，请说明":"后面的数字是什么含义
```
 /* Gate descriptors for interrupts and traps */
 struct gatedesc {
    unsigned gd_off_15_0 : 16;        // low 16 bits of offset in segment
    unsigned gd_ss : 16;            // segment selector
    unsigned gd_args : 5;            // # args, 0 for interrupt/trap gates
    unsigned gd_rsv1 : 3;            // reserved(should be zero I guess)
    unsigned gd_type : 4;            // type(STS_{TG,IG32,TG32})
    unsigned gd_s : 1;                // must be 0 (system)
    unsigned gd_dpl : 2;            // descriptor(meaning new) privilege level
    unsigned gd_p : 1;                // Present
    unsigned gd_off_31_16 : 16;        // high bits of offset in segment
 };
```
    * ":"后面的数字表示该struct中对应成员的位宽度，即占用多少bit。
    

- 对于如下的代码段，

```
#define SETGATE(gate, istrap, sel, off, dpl) {            \
    (gate).gd_off_15_0 = (uint32_t)(off) & 0xffff;        \
    (gate).gd_ss = (sel);                                \
    (gate).gd_args = 0;                                    \
    (gate).gd_rsv1 = 0;                                    \
    (gate).gd_type = (istrap) ? STS_TG32 : STS_IG32;    \
    (gate).gd_s = 0;                                    \
    (gate).gd_dpl = (dpl);                                \
    (gate).gd_p = 1;                                    \
    (gate).gd_off_31_16 = (uint32_t)(off) >> 16;        \
}
```
如果在其他代码段中有如下语句，
```
unsigned intr;
intr=8;
SETGATE(intr, 1,2,3,0);
```
请问执行上述指令后， intr的值是多少？

- intr值为 0x00020003(x86为小端)