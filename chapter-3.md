# 第三章 存储管理

## 存储管理的主要技术
- 基本存储管理（连续分配）
    - 单一连续分配
    - 固定分区分配
    - 动态分区分配
    - 动态重定位分区分配
    - 交换swapping技术
    - 分页存储管理
    - 分段存储管理
    - 段页式存储管理
- 虚拟存储管理
    - 请求分页系统
    - 请求分段系统

## 连续分配存储管理（1960~1979）
- 是指为一个用户程序分配一个连续的内存空间
- 两种方式
	- 单一连续分配方式
    	- 最早出现的一种存储管理方式内存中仅驻留一道程序，整个用户区为一用户独占
	- 分区式分配方式
		- 适用于多道程序的简单存储管理方式
		- 两种方式
    		- 固定分区式
    		- 动态分区式，又称可变分区
        		- 动态分区分配：可变分区
        		- 动态重定位分区分配：可再定位式分区分配,浮动分区
        		- 多重式分区分配
		- 内存零头，即碎片

## 内存压缩/内存紧缩
- (memory compaction)
- 内存分区多次变动后，出现不能被利用的小分区称为“碎片”或“零头”
- 将所有的进程向下或向上移动，使它们相邻接，将碎片整合成大的分区，称之为紧缩或拼接或紧凑
- 经过紧缩后，用户进程在内存中的位置发生了变化，若不对程序和数据的地址进行修改，则程序将无法执行，为使之能执行，必须进程重定位

## 分页系统
页表 一级页表 二级页表 多级页表 翻转页表
TLB 块表

## 分段系统
段号 + 段内偏移量

## 局部性原理
- 时间局限性：若程序中的某条指令一旦执行，则不久以后该指令可能再次执行；如果某个数据结构被访问，则不久以后该数据结构可能再次被访问；
- 空间局限性：一旦程序访问了某个存储单元，在不久以后，其附近的存储单元也被访问。

## MMU 内存管理单元
管理虚拟存储器、物理存储器的控制线路，同时也负责虚拟地址映射为物理地址

## 虚拟存储器的特征
- 离散性
    - 指在内存分配时采用离散分配方式，这是最基本的特征，没有此也就没有虚拟存储器
- 多次性
    - 指一个作业被分成多次地调入内存运行，这是虚拟存储器的最重要的特征
- 对换性
    - 指允许在作业的运行过程中换进、换出，从而有效地提高内存利用率
- 虚拟性
    - 指能够从逻辑上扩充内存容量，使用户所看到的内存容量远远大于实际内存容量。

## 页面置换算法
- LRU
- FIFO
- 最优（最远将来）

## 抖动
- 置换算法的好坏将直接影响系统的性能，不适当的算法可能会导致进程发生“抖动”
- 即刚刚被换出的页很快又被访问，需要重新调入，为此，又需再选一页调出；而此刚被换出的页，很快又要被访问，因此又需要将它调入，如此频繁地更换页面，以至一个进程在运行中，将把大部分时间花费在完成页面置换的工作上
- 一个好的页面置换算法，应具有较低的页面更换频率

### 产生抖动的原因
- 抖动原因1：在请求分页系统中，通常每一个进程都只能分配到它所需要的全部内存空间中的一部分；如果进程A在运行过程中需要增加页面，它便会产生中断。在采用全局置换策略时，实际上它有可能是被分配到一个原属B进程的物理块，用来装入新调入的页。而B进程在运行中还需要该物理块，因而也会产生缺页中断，于是有可能又获得了C进程的一个物理块，等等。而这些产生缺页中断的进程，可能会因页面的调入/调出而处于等待状态，从而导致就绪队列空。
- 抖动原因2：通过对CPU的监督，一旦调度程序发现CPU的利用率降低时，应立即提高多道程序度，以提高CPU的利用率。为此，需引入新的进程参加运行。当新进程进入内存时，客观上只能从其它运行进程处去获得物理块，于是又将进一步加剧了进程的缺页情况，使等待页面调入/调出的进程数目也随之增多，这又将使CPU的利用率进一步下降。又去引入新的进程，形成恶性循环。
- CPU的利用率与多道程序度之间的关系，先增后减。
- 为了保证系统有较大的吞吐量，必须防止发生抖动

### 产生抖动的预防
1. 采取局部置换策略
    - 系统采取可变分配方式，且采取局部置换策略
    - 当某进程发现缺页后，仅在自己的内存空间范围内置换页面，不允许从其它进程获得新的物理块。这样即使有某个进程发生抖动，也不至于引起其它进程也产生抖动
    - 该策略不能从根本上防止抖动的发生
2. 在CPU调度程序中引入工作集算法
    - 是否驻留集足够大
3. L=S准则(Denning,1980)
    - 其用来调整多道程序度，以使产生缺页的平均时间L等于系统处理进程缺页的平均时间S。此时CPU的利用率最好。
4. 挂起若干进程
    - 当多道程序度偏高时，为了防止发生“抖动”，挂起一些进程，以便腾出内存空间来分配给抖动的进程。
    - 被挂起的进程通常是选择优先权最低或较低的；
    - 当内存非常拥挤时，也可以选择一个并不很重要的，但确较大的进程挂起，以便能一次释放出较大的内存空间；
    - 或将具有最多剩余执行时间的进程挂起。

## 磁盘臂调度算法
- FIFO
- 最短调度优先
- 电梯算法




