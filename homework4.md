*   思考题

4.1 启动顺序

*   段寄存器的字段含义和功能有哪些？
    * CS:code segment, 当前程序代码所在段的基址；
    * SS:stack segment, 当前使用堆栈所在段的基址；
    * DS:data segment, 当前使用数据所在段的基址；
    * ES:extra segment, 当前使用附加数据段的段基址。

*   描述符特权级DPL、当前特权级CPL和请求特权级RPL的含义是什么？在哪些寄存器中存在这些字段？对应的访问条件是什么？
    * DPL:代码所属的特权等级，也就是真正的特权级；存在对应描述符的权限位，对应程序正常情况下访问条件、
    * CPL:当前正在执行的代码的特权等级，存储在CS的最低2位，对应程序当前状态可获得的最大权限；
    * RPL:程序正请求的特权等级，存储在段选择子中，对应程序当前请求的特权等级，与CPL共同构成需求的特权级。
*   分析可执行文件格式elf的格式（无需回答）

4.2 C函数调用的实现
4.4 x86中断处理过程

*   中断处理中硬件压栈内容？用户态中断和内核态中断的硬件压栈有什么不同?
    * 硬件要依次压栈EFLAGS, CS, EIP, error code；
    * 内核态中断如上；用户态中断还要在之前额外压栈SS, ESP来为将来堆栈切换回去准备。

*   为什么在用户态的中断响应要使用内核堆栈？
    * 因为中断处理例程需要在内核态下运行，所以需要先切换到内核态，这就使用了内核堆栈。

*   trap类型的中断门与interrupt类型的中断门有啥设置上的差别？如果在设置中断门上不做区分，会有什么可能的后果?
    * interrupt类型的中断门需要关中断，而trap类型不需要。
    * 如果不做区分，可能导致trap类型的中断门处理过程中丢失外设中断。

4.8 练习四和五 ucore内核映像加载和函数调用栈分析

*   在kdebug.c文件中用到的函数read_ebp是内联的，而函数read_eip不是内联的。为什么要设计成这样？
    * 因为ebp寄存器可以直接读取，而eip不行。项目中读取eip的方法是在call函数后读取栈中的old eip，这必须要使用函数调用过程，所以不能做成内联。

4.9 练习六 完善中断初始化和处理

*   CPU加电初始化后中断是使能的吗？为什么？
    * 不是使能的，因为未设置中断表。