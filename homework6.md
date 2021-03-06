# 思考题

6.1 非连续内存分配的需求背景

* 为什么要设计非连续内存分配机制？
    * 允许程序使用非连续地址空间；允许共享代码；允许动态加载和动态链接。

* 非连续内存分配中内存分块大小有哪些可能的选择？大小与大小是否可变?
    * 段式分配，块大小大，可变；
    * 页式分配，块大小小，不可变。

* 为什么在大块时要设计大小可变，而在小块时要设计成固定大小？小块时的固定大小可以提供多种选择吗？
    * 大块时需要适应不同需求，所以大小需要可变；
    * 小块时可以通过自由组合满足需求，所以设计成固定大小方便管理；
    * 可以根据各种情况提供不同选择。

6.2 段式存储管理

* 什么是段、段基址和段内偏移？
    * 段：一个逻辑实体，存放逻辑上相关（访问方式、数据类型相同）的数据或程序；
    * 段基址：段开始的物理地址；
    * 段内偏移：对应内存单元距离段基址的偏移。

* 段式存储管理机制的地址转换流程是什么？为什么在段式存储管理中，各段的存储位置可以不连续？这种做法有什么好处和麻烦？
    * 流程：将逻辑地址分为段描述符和段内偏移；通过段描述符在段表中找到相应段基址；将段基址与段内偏移相加得到实际物理地址。
    * 不连续原因：相邻段描述符对应段不一定相邻；
    * 好处：允许不连续内存分配；将内核与用户、程序与数据分开。
	* 麻烦：地址转换更复杂
    
6.3 页式存储管理

* 什么是页（page）、帧（frame）、页表（page table）、存储管理单元（MMU）、快表（TLB, Translation Lookaside Buffer）和高速缓存（cache）？
    * 页：虚拟地址中的一块固定大小空间；
    * 帧：物理内存中的页面；
    * 页表：储存页与帧映射的表；
    * MMU：负责管理内存的元件，包含TLB等部分；
    * 快表：硬件上维护页与帧映射的结构，能够快速找到页对应的帧；
    * 高速缓存：硬件上对内存的缓存，能够储存最近使用的数据，减少内存寻址，加快速度。
* 页式存储管理机制的地址转换流程是什么？为什么在页式存储管理中，各页的存储位置可以不连续？这种做法有什么好处和麻烦？
    * 流程：将逻辑地址分为页号和页内偏移；通页号在页表中找到相应帧基址；将帧基址与页内偏移相加得到实际物理地址。
    * 不连续原因：虚拟页号与帧基址无必然联系；
    * 好处：允许不连续内存分配，方便分配与回收；页大小相同，便于管理与分配。
	* 麻烦：地址转换复杂，频繁访问内存

6.4 页表概述

* 每个页表项有些什么内容？有哪些标志位？它们起什么作用？
    * 标志位，帧号；
    * 标志位包括存在位（是否存在对应物理帧）、修改位（是否修改过）、引用位（是否有程序正在使用）
* 页表大小受哪些因素影响？
    * 页大小、地址空间大小、进程数

6.5 快表和多级页表

* 快表（TLB）与高速缓存（cache）有什么不同？
    * 快表可以看作是页表的cache。
    * 快表主要用于页表缓存，cache缓存一切内存数据副本。
    * 两者结构不同且cache一般大小大于TLB。
    * TLB位于MMU中，而cache不是。
* 为什么快表中查找物理地址的速度非常快？它是如何实现的？为什么它的的容量很小？
    * 快表中通过硬件级别的比较器与选择器同时比较页号的各位，速度比软件实现的页表快；
    * 如果TLB容量增大，可能会导致频繁的TLB更新，反而减慢速度。
    * TLB容量过大会导致成本迅速上升。
* 什么是多级页表？多级页表中的地址转换流程是什么？多级页表有什么好处和麻烦？
    * 多级页表可以将低级页表的信息储存在高级页表中，形成树状结构；
    * 流程：将逻辑地址分为描述符与多级偏移，先用描述符找到最高级页表，然后循环通过偏移在当前页表中寻找下级页表，最后通过页内偏移获得实际物理地址。
    * 好处：不需要为所有页建立页表，节省空间；
    * 缺点：多次寻址，减慢速度。

6.6 反置页表

* 页寄存器机制的地址转换流程是什么？
    * 页寄存器会储存当前进程使用的页表起始地址，获得虚拟地址后，首先分成页号和页内偏移，然后从页表起始地址获取页号对应的物理帧，最后加上页内偏移得到物理地址。
* 反置页表机制的地址转换流程是什么？
    * 反置页表储存每个物理帧所对应的虚拟页与其所在进程。获得虚拟地址后，首先分成页号和页内偏移，然后计算页号哈希值，由此寻找对应项，如果进程和页号都符合则获得帧号，否则延next项继续寻找直至找到或失败为止；
    * 最后，将帧基址与偏移组合获得物理地址，或报缺页错误。
* 反置页表项有些什么内容？
    * 标志位、物理帧对应的进程号、虚拟页号、next项。

6.7 段页式存储管理

* 段页式存储管理机制的地址转换流程是什么？这种做法有什么好处和麻烦？
    * 先将虚拟地址分为段号、页号、页内偏移，然后依次寻找对应段、对应物理帧，加上偏移得到物理地址。
    * 好处：集合了段式与页式的优点，能够同时蛮族段式的分隔功能和页式的便于管理。
* 如何实现基于段式存储管理的内存共享？
    * 硬件实现，由存储保护键和界限寄存器实现。
    * 将要共享的部分段基址设为相同。
* 如何实现基于页式存储管理的内存共享？
    * 使相关进程的逻辑空间中的页指向相同的内存块，并设置相关存储保护。
