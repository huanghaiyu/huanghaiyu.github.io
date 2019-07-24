---
layout: post
title: zynq 7000 mutex
categories: 
 - zynq 7000
permalink: /zynq_7000/mutex
nocomments: false  	# enable the comment box. This is EasyBook feature
---

在多处理器环境中，处理器共享通用资源。Mutex core提供了一种互斥机制，使一个进程能够获得对特定资源的独占访问。
Mutex core包含可配置数码的mutex，例如16个。每一个mutex分配一个32bit的用户配置寄存器用来存储任意的数据（例如被mutex保护的共享资源的地址）。

# Mutex Core的系统框图

Mutex 模块通过AXI4-Lite系统总线和其他模块连接（processor,etc）。

![]({{ "/public/upload/mutex_axi4-lite_system.png" | absolute_url }})

Mutex core有多个（最大支持8个）总线接口来访问core内部资源，在多core处理器系统中通常和不同的处理器链接。Mutex core心提供如下类型的寄存器，这些寄存器通过AXI4-Lite总线接口访问:

- Mutex core寄存器，用于提供获取mutex和释放mutex的功能。
- 用户配置寄存器，用于存储任意的数据。

Mutex core 同时提供一种硬件保护机制，用于mutex访问的硬件防干扰保护，防止除预期的处理器之外的任何处理器修改mutex。这种机制基于HWID实现，HWID由锁定mutex的处理器的AXI接口号和AXI事务ID组成。

注释：以zynq 7000 双核AMP 架构为例，Mutex Core一般有两种使用模式：
1. cup0 通过一个AXI-Lite总线接口访问Mutex Core, cpu1 通过另一个总线接口访问Mutex Core。
2. cpu0和cpu1通过同一个AXI-Lite总线接口访问Mutex Core，由AXI 内部连接器仲裁双核的访问。

# 寄存器空间
	
每一个Mutex core的interface可以访问所有的mutexs。但是，在同一时间只有一个interface可以访问任何一个mutexs。例如，当一个interface正在访问任何一个mutexs的时候，所有其他AXI接口被阻塞住。Interface仲裁有固定的优先级，AXI0到AXI7优先级递减的顺序，例如S0_AXI有最高的优先级，S7_AXI有最低的优先级。
当配置多个mutexs时，每个mutex都保留一个256字节的地址范围，也就是说，mutex#0的寄存器位于0x0和0xFF之间，mutex#1位于0x100和0x1FF之间，以此类推。

# Mutex 寄存器

MUTEX寄存器包含一个强制位域和两个可选位域。锁定位是必需的，因为这个位决定mutex是否处于锁定或释放状态。通常包含CPUID来控制谁可以操纵mutex的访问。只有mutex的所有者才能释放它。为了更加安全，还提供了一个可选的HWID字段。HWID位不是用户可访问的，而是在后台隐式处理。HWID包含哪一个AXI主设备访问mutex。这个机制保证不会有其他的处理器可以伪造CPUID，通过mutex获取访问权限。

CPUID是由工具分配给在每个处理器上执行的软件的惟一标识值。因为CPUID只分配给SDK中创建的软件，所以任何访问mutex的其他主机都必须手动分配一个不干扰其他master的唯一编号。

![]({{ "/public/upload/mutex_register_space.png" | absolute_url }})
 
# Mutex 上锁和释放的过程
对于一个空闲的mutex，MUTEX寄存器是0.

**Mutex上锁过程：**

1. 向MUTEX寄存器写入<CPUID & 1>；CPUID表示当前想要获取锁的CPU（线程），对应到MUTEX寄存器中的CPUID域 bit1-bit8；1表示mutex当前的拥有者是CPUID。如果硬件保护C_ENABLE_HW_PROT被使能，HWID同样被用来增强保护。**如果mutex已经被锁上，这个访问被mutex忽略（Mutex core硬件逻辑无效掉当前写操作，依据访问的CPU ID)。**
2. 读回MUTEX寄存器的值与写入的值比较，校验mutex是否已经被当前的CPU获取。如果不一致，重复步骤a和步骤b，直到获取的mutex。
3. 到这里CPU就可以访问被保护的共享资源了。

**释放Mutex过程：**

1. 向MUTEX寄存器写入<CPUID & 0>；CPUID表示当前想要获取锁的CPU（线程），对应到MUTEX寄存器中的CPUID域 bit1-bit8；0表示mutex当前被CPUID 释放。如果硬件保护C_ENABLE_HW_PROT被使能，HWID同样被考虑到。Mutex自动将MUTEX寄存器置零。(只有mutex的所有者才能释放它)

如果“错误”的处理器尝试访问空闲的mutex，同时硬件保护C_ENABLE_HW_PROT被使能，并且使用正确的CPUID，这个操作被忽略，因为对于这个操作只有HWID和CPUID必须1匹配上才会成功。同样，“正确”的处理器写入错误的CPUID，这个操作也会被忽略。