# 思考题

5.1 计算机体系结构和内存层次

* 1.操作系统中存储管理的目标是什么？
    * 对主存储器中用户部分的数据进行管理；
    * 实现内存的分配与回收；
    * 抽象出逻辑地址空间，方便编写程序；
    * 保护每个程序的独立地址空间；
    * 允许不同程序访问相同内存；
    * 将内存地址虚拟化，提供更大的地址空间。

5.2 地址空间和地址生成

* 1.描述编译、汇编、链接和加载的过程是什么？
    * 编译：将高级语言的源程序翻译成汇编语言程序；
    * 汇编：将汇编语言程序翻译成由机器语言指令组成的二进制文件；
    * 链接：合并程序代码与使用的外部库代码，生成可执行文件；
    * 加载：将可执行文件从磁盘加载进内存，将相对地址翻译成绝对地址开始执行。

* 2.动态链接如何使用？尝试在Linux平台上使用LD_DEBUG查看一个C语言Hello world的启动流程。 (optional)

5.3 连续内存分配

* 1.什么是内碎片、外碎片？ 
    * 内碎片：分配单元内部未被使用的内存；
    * 外碎片：分配单元之间未被使用的内存。
* 2.最先匹配会越用越慢吗？请说明理由（可来源于猜想或具体的实验）？ 
    * 会。最先匹配会在低地址空间产生大量碎片，而匹配时需要从0地址遍历所有空闲块，这些碎片会影响遍历效率。
* 3.最差匹配的外碎片会比最优适配算法少吗？请说明理由（可来源于猜想或具体的实验）？ 
    * 会。相对来说，最差匹配每次分配后产生的新空闲块大小较大，更不容易变成碎片。
* 4.理解0:最优匹配，1:最差匹配，2:最先匹配，3:buddy systemm算法中分区释放后的合并处理过程？ (optional)

5.4 碎片整理

* 1.对换和紧凑都是碎片整理技术，它们的主要区别是什么？为什么在早期的操作系统中采用对换技术？
    * 主要区别是紧凑只在内存内部移动，对换会将内存中数据对换至磁盘。
    * 早期操作系统中没有实现程序的动态重定位，所以无法实现紧凑，只能采用对换技术；
    * 另一方面，早期操作系统中内存较少，经常被占满，紧凑意义不大。
* 2.一个处于等待状态的进程被对换到外存（对换等待状态）后，等待事件出现了。操作系统需要如何响应？
    * 在等待时间更长且已就绪的进程都被换入内存后，换入该进程。

5.5 伙伴系统

* 1.伙伴系统的空闲块如何组织？
    * 按大小和起始地址组织成二维数组。
* 2.伙伴系统的内存分配流程？伙伴系统的内存回收流程？
    * 分配：由小到大在空闲块数组中找最小的可用空闲块；如空闲块过大，对可用空闲块进行二等分，直到得到合适的可用空闲块。
    * 回收：把释放的块放入空闲块数组；合并满足合并条件的空闲块。
    * 合并条件为大小相同，地址相邻，且低地址空闲块起始地址为该块大小*2的倍数。

