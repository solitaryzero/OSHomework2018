8.1 虚拟存储的需求背景

- 寄存器、高速缓存、内存、外存的访问特征？
	- 储存容量递增，成本递减，访问时间递增。

- 如何理解计算机中的存储层次结构所的理想状态是“均衡繁忙”状态？
	- 如果过于依赖低级存储，低负载时会减慢速度；过于依赖高级存储则会导致高负载时系统无法正常工作。
	- 根据短板理论，系统总效率取决于负载最重的层次，所以理想状态是各层次都有均衡的负载。
	
- 在你写程序时遇到过内存不够的情况吗？尝试过什么解决方法？
	- 有。优化数据结构、分批处理数据，使用mmap将内存映射到外存。

8.2 覆盖和交换

- 什么是覆盖技术？使用覆盖技术的程序开发者的主要工作是什么？
	- 使程序中逻辑上没有关联的程序块共用一块内存，从而降低内存开销。
	- 划分程序逻辑块并进行分类。

- 什么是交换技术？覆盖与交换有什么不同？
	- 交换技术指内存不够时，将较少使用的内存块换出到外存中，再分配给新需求。
	- 覆盖会清除原数据，而交换只是暂时从内存移出数据。

- 覆盖和交换技术在现代计算机系统中还有需要吗？可能用在什么地方？
	- 需要。可能用在多进程上，允许每一个进程拥有虚拟的地址空间。

- 如何分析内核模块间的依赖关系？
	- 编译器分析，手动设置。

- 如何获取内核模块间的函数调用列表？
	- 编译器，文档。

8.3 局部性原理

- 什么是时间局部性、空间局部性和分支局部性？
	- 时间局部性：如果一个信息项正在被访问，那么在近期它很可能还会被再次访问。
	- 空间局部性：在最近的将来将用到的信息很可能与现在正在使用的信息在空间地址上是临近的。
	- 分支局部性：程序执行的时候访问数据块一般是顺序访问或者隔一个空间地按序访问(if-else)。

- 如何提高程序执行时的局部性特征？
	- 将大块数据一次性读写，按顺序访问数据,etc.

- 排序算法的局部性特征？
	- 例：插入排序总是按顺序扫描数据，且经常反复访问前面部分数据。

- 参考：九大排序算法再总结

8.4 虚拟存储概念

- 什么是虚拟存储？它与覆盖和交换的区别是什么？它有什么好处和挑战？
	- 虚拟存储：只把当前指令执行所需要的部分进程地址空间内容放在物理内存中。
	- 好处：数据不连续，用户空间大。
	- 挑战：需要进行部分交换。

- 虚拟存储需要什么样的支持技术？
	- 地址转换，置换算法。

8.5 虚拟页式存储

- 什么是虚拟页式存储？缺页中断处理的功能是什么？
	- 虚拟页式存储指将程序的逻辑地址空间划分为固定大小的页，每一个页可以对应任意一个物理帧，物理帧不必连续，以此实现了离散存储。
	- 缺页中断处理功能：将外存中对应的物理帧换入，满足程序访问要求。

- 为了支持虚拟页式存储的实现，页表项有什么修改？
	- 加入以下标志位：
	- 存在位：是否在内存；
	- 修改位：是否修改过；
	- 访问位：是否被访问过；
	- 保护位：允许的读写和访问方式。

- 页式存储和虚拟页式存储的区别是什么？
	- 页式存储针对物理内存，虚拟页式存储针对虚拟内存；
	- 一个物理帧可以对应多个虚拟页；
	- 页式存储不需要进行覆盖和交换。

8.6 缺页异常

- 缺页异常的处理流程？
	- 程序查询页表，抛异常，通过中断进入内核，内核将外存中的页面换入，修改页表，结束中断。

- 虚拟页式存储管理中有效存储访问时间是如何计算的？

- 如何处理缺页中断中的缺页中断？
	- 页表项占的页无法被换出，防止嵌套中断。
