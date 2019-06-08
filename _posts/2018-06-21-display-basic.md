---
layout: post
title: 显示技术概述
categories: 
 - Blog
 - Technology
permalink: /posts/2
nocomments: true  # Disable the comment box. This is EasyBook feature
---
介绍一些显示技术基础知识

* [1.显示技术概述——显示技术发展史](#1)
	* [1.0 CRT历史背景](#1.0)
	* [1.1 CRT组成组件](#1.1)
	* [1.2 一帧画面是如何形成的](#1.2)
	* [1.3 扫描方式和帧结构](#1.3)
* [2.显示技术概述——显示相关的硬件模块](#2)
* [3.显示技术概述——显示接口协议](#3)
	* [3.0 LCD](#3.0)
		* [3.0.0 RGB](#3.0.0)
		* [3.0.1 LVDS](#3.0.1)
		* [3.0.2 EDP](#3.0.2)
		* [3.0.3 MIPI-DSI](#3.0.3) 
	* [3.1 TV](#3.1)
		* [3.1.0 HDMI](#3.1.0)
		* [3.1.2 CVBS](#3.1.2)

# 1. 显示技术概述——显示技术发展史

**前言**：了解显示技术发展史，有助于理解当今显示技术中规范的一些技术参数、相关术语，比如什么是帧结构？帧结构有什么组成？为什么要由这些东西组成？ 等等。

## CRT 问世
### 1.0 **CRT历史背景：**
	
	1897，诺贝尔奖获得者卡尔·布劳恩，发明出了第一个CRT(阴极射线管)。

### 1.1 **CRT组成组件**：

	a) CRT显示终端主要由电子枪（Electron gun）：发射高速电子，有三束，会分别轰击荧光粉层中像素单元的R、G、B三原色。

	b) 偏转线圈(Deflection coils）：用来协助三束电子束快速的对像素进行激发。

	c) 荫罩(Shadow mask）:保证三支电子束在扫描过程中，准确击中每一个像素。

	d）荧光粉层（phosphor）：荧光粉层由荧光粉单元组成，相邻的红、绿、蓝荧光粉单元为一组，称之为像素。

	e) 玻璃外壳。

### 1.2 **一帧画面是如何形成的**：

![](http://i.imgur.com/xiSPaIP.gif)

首先画面显示的原理利用了人眼的视觉残留特性。一支电子枪，发射出三支电子素，分别去按照一点比例轰击每个像素；在偏转线圈和荫罩的帮助下，以非常快的速度，周期性的对每个像素进行扫描激发，让每个像素都发光，并且这个周期足够短（足以欺骗人的眼睛，比如40ms以内），这样我们就会看到一幅完整的图像。

### 1.3 **扫描方式和帧结构**：

a) 电子束从屏幕左上角开始，向右扫一条水平线(scan line)，然后迅速地回扫（retrace）到左边偏下一点的位置，然后开始第二条水平线扫描，直到最后一条水平线，完整的一帧扫描结束。

![](http://i.imgur.com/H9vcryY.png)

b) 从下图我们可以看到整个时序图可以分为四段，这四个阶段也组成了帧结构：

![](http://i.imgur.com/LTBAjLu.png)

	> **显示阶段**，也就是video\_on使能的阶段。这个时候controlller要将RGB数据送给显示器，而且是每一个pixel_clk送出一个pixel的RGB数据。

	> **折回（retrace）** 也就是电子枪扫完一行之后回到最初扫描的位置用的时间，在这个时间段内，电子枪是没有任何电子束发出的。

	> **前沿（front porch**），之所以称为前沿，是因为它在一行扫描方向的前方，显示右边黑掉的部分，此时电子枪已经关闭，但是电子束还有残留发射。

	> **后沿（back porch）**，之所以称为后沿，是因为它在一行扫描方向的后方，显示左边黑掉的部分。

**Information is only displayed when the beam is moving in the “forward” direction (left to right and top to bottom)，and not during the time the beam is reset back to the left or top edge of the display。** 只有当电子束在“向前”方向（从左到右和从上到下）移动时，信息才会显示，而不是在电子束被重新设置到显示屏的左边缘或上边缘的时间内显示。
	
这里有个很重要的信息是**not during the time the beam is reset back to the left or top edge of the display**，言外之意是，在这个阶段电子束是不能发射出来电子的，当电子枪扫描到一行结尾时候，需要关闭电子束，准备折回到下一行，关闭电子束是需要时间的，这个时间就是图中右边灰色区域**front porch**的时间，如果不预留这个时间，立即折回电子枪，那么未完全关闭的电子束很有可能就会轰击到屏幕上，从而产生异常显示；同理，当电子枪折回到下一行开头的位置后，开启电子束也是需要时间的，这个时间就是图中左边灰色区域back porch的时间，在这个时间内电子束完全稳定后，开始真正的一行数据显示，图中display时间段内。

图中的锯齿图，表示了电子枪的运动轨迹，斜率为正，代表电子枪从左往右（或从上向下移动），斜率为负，代表电子枪从右往左（或从下往上移动）

由于这些历史原因，在后面发展的LCD等显示panel的时序中也沿用了这样的帧结构组成。

# 2. 显示技术概述——显示相关的硬件模块

# 3. 显示技术概述——显示接口协议

## 3.0 LCD
**TFT-LCD显示原理：**

![](http://i.imgur.com/fdcTAgC.png)

TFT-LCD 的构造是在两片平行的玻璃基板当中放置液晶盒，下基板玻璃上设置TFT（薄膜晶体管），上基板玻璃上设置彩色滤光片，通过TFT上的信号与电压改变来控制液晶分子的转动方向，从而达到控制每个像素点偏振光出射与否而达到显示目的。

**TFT-LCD常用的接口:**

RGB、LVDS、MCU（CPU）、MIPI（DSI）、EDP。

### 3.0.0 RGB
- **并口RGB接口概述**

RGB接口也称之为TTL接口，它的特点是RGB数据采用**并行方式传输**，就是说每一bit的RGB数据，就需要一根数据线来发送。TTL接口信号电压高、连线多、传输电缆长，因此，电路的抗干扰能力比较差，而且容易产生电磁干扰（EMI）。
在实际应用中，TTL接口电路多用来驱动**小尺寸**（15in以下）或**低分辨率**的液晶面板。另外，在笔记本电脑中也常使用TTL接口形式。

目前主要有16bit(RGB565)、18bit(RGB666)、24bit(RGB888)三种接口。

- **RGB接口组成**

有两部分组成：

a) RGB控制器，一般集成在SOC上面。

b) RGB panel，就是经常说的液晶屏幕，屏幕内部集成有Driver IC，和RGB控制连接。

![](http://i.imgur.com/2YcCglD.png)

RGB接口中一般包含**RGB数据信号**、**时钟信号和控制信号（DE，HS，VH）**这三大类信号，
下面以24bit(RGB888)接口为例分析，先看下面一张RGB屏的spec中对接口引脚的部分定义：


![](http://i.imgur.com/MglsSh7.png)


**MODE 引脚：** 用来选择工作模式，拉高为DE模式，拉低为SYNC模式。拉这个引脚一般会通过硬件的在上电初始化的时候拉高或者拉低。

**DEN 引脚 ：**DE模式下的数据是能信号引脚。在DE模式下，这个引脚的电平发生变化的时候，预示着即将开始传输RGB数据，一般是行数据，这个变化可以是由高电平跳变到低电平，也可以是有低电平跳变到高电平。

**HSD、VSD 引脚：** SYNC模式下行输入的同步信号和帧输入的同步信号。在SYNC模式下，两个引脚上的信号分别用来同步一行数据的传输和一帧数据的传输。

**DCLK 引脚 ：** 像素时钟引脚，在每一个时钟周期内将进行一个像素数据的传输（RGB888接口的模式是传输24bit，RGB666接口的模式是传输18bit,RGB565接口模式是传输16bit）。

**R0~7、G0~7、B0~8引脚：** RGB数据引脚，这些引脚上的信号将在控制信号的约束下进行RGB数据的传输。

以上引脚信号都是数字信号。

- **RGB接口时序**

	**/\*TODO\*/**


- **串行RGB接口概述**


- **串行RGB接口组成**

![](https://i.imgur.com/QwkaXLs.png)

![](https://i.imgur.com/912hdhD.png)

以上引脚信号都是数字信号。

- **RGB接口时序**



- **Linux RGB设备驱动模型**

	**/\*TODO\*/**

### 3.0.1 LVDS

- **LVDS接口概述：**
	
	LVDS接口接口是一种视频信号传输模式，也是是一种电平标准，采用串行方式传输，广泛应用于液晶屏接口。

	目前平板市场使用最广泛的，尺寸从7寸到10.1寸,分辨率从1024x600到1920x1200。LVDS分单通道跟双通道，一般1280x800以下分辨率使用单通道，1920x1080分辨率以上的使用双通道。单通道需要数据引脚为10个，而双通道需要20个。

- **LVDS接口组成：**

	LVDS接口属于**串行传输数据接口**，接口中一般包含若干数据差分信号(数据信号和控制信号)和时钟差分信号。（一个时钟脉冲内，每组数据线可传输7bit数据），LVDS接口有两部组成：

	a) soc上集成的LVDS控制器(LVDS发送端)

	b) 液晶面板侧的LVDS输入接口电路（LVDS接收端）。

	![](http://i.imgur.com/0ekFu8S.png)	

	![](http://i.imgur.com/ArnQkvC.png)

LVDS控制器将并行RGB数据信号和控制信号转换成低电压串行LVDS信号，然后通过连接排线将信号传送到液晶面板的LVDS接收器（driver IC），液晶面板的driver IC再将串行信号转换为TTL电平的并行信号输出，除了包括RGB数据信号外，还包括行同步、场同步、像素时钟等信号。

**注：**双通路的奇偶通道各自有一路时钟信号控制，上图中的(CLKOUT_M、CLKOUT_P), (CLKOUT_M1、CLKOUT_P1)。以1920x1080分辨率双通路LVDS为例，像素时钟大概在140MHz左右，那么平分到每一路上的时钟减半，为70MHz，这一部分硬件电路通常有LVDS控制器内部实现。

- **LVDS接口时序：**

LVDS信号传输分为**DE MODE和SYNC MODE**，DE mode需连接DE信号（data enable有效数据选通），SYNC mode还需连接HS（HSYNC行同步）、VS（VSYNC场同步）。SYNC mode在现在的panel中已很少使用。下面是DE mode的数据形式。

**a) DE mode 时序图：** 

![](http://i.imgur.com/DQviH89.png)

**b) SYNC mode：**

**c）LVDS 数据传输时序：** 

LVDS是串行接口，RGB信号传输时，是将每个基色信号的数据排成一纵队，采用差分数据线按顺序进行输出。**在一个时钟脉冲周期内，一对差分数据线可以传输7bit数据，**如下图所示。 

![](http://i.imgur.com/3aidt2r.png)

**单路6 bit LVDS**

对于单路6bit LVDS接口，需要3对差分数据线，即RX0-和RX0十，RX1-和RX1＋，RX2-和RX2＋。因每对差分数据线可以传输7bit数据，这样，3对差分数据线可以传输3×7bit＝21 bit，除R0～R5、G0～G5、B0～B5占去18bit，还剩下3bit用于传输HS（行同步）、VS（场同步）、DE（有效数据选通）信号（若HS、VS信号不传输，将空余2bit）。

![](http://i.imgur.com/3H66Yeu.png)

**双路6 bit LVDS**

对于双路6bit LVDS接口，需要6对差分数据线，其中，奇路3对，即RX00-和RX00＋，RX01- 和RX01＋，RXO2-和RX02＋；偶路3对，即RXE0-和RXE0＋，RXE1-和RXE1＋，RXE2-和RXE2＋。这6对差分数据线可以传输6×7bit＝42bit，除奇路（OR0～OR5、OG0～OG5、OB0～OB5）和偶路（ER0～ER5、EG0～EC5、EB0～EB5）占去36bit，还剩下6bit，HS、VS、DE信号占3bit，还空余3bit（若HS、VS信号不传输，将空余5bit）。

![](http://i.imgur.com/cQpnX7y.png)

**单路8 bit LVDS**

对于单路8bit LVDS接口，需要4对差分数据线，即RX0-和RX0＋，RX1-和RX1＋，RX2-和RX2＋，RX3-和RX3＋。因每对差分数据线可以传输7bit数据，这样，4对差分数据线可以传输4×7bit=28bit，除R0～R7、G0～G7、B0～B7占去24bit，还剩下4bit，HS、VS、DE占3bit，还空余1 bit（若HS、VS信号不传输，将空余3bit）。

![](http://i.imgur.com/fbgJNTR.png)

**双路8 bit LVDS**

对于双路8bit LVDS接口，需要8对差分数据线，其中，奇路4对，即RX00-和RX00＋，RX01-和RX01＋，RX02-和RX02＋，RX03-和RX03＋；偶路3对，即RXE0-和RXE0＋，RXE1-和RXE1＋，RXE2-和RXE2＋，RXE3-和RXE3＋。这8对差分数据线可以传输8×7bit＝56bit，除奇路（OR0～OR7、OG0～OG7、OB0～OB7）和偶路（BR0～ER7、EG0～EG7、EB0～EB7）占去48bit，还剩下8bit，HS、VS、DE信号占3bit，还空余5bit（若HS、VS信号不传输，将空余7bit）。

![](http://i.imgur.com/mZ9VRXM.png)


- **Linux LVDS驱动模型**

在Linux设备模型中，soc 上集成的controller归类为platform 设备，所以LVDS控制器设备驱动的编写，按照platform框架来实现。

至于LVDS panel端驱动，主要需要实现屏的上电时序，也可以将LVDS panel抽象出来，遵循platform框架加入到linux驱动模型中。


### 3.0.2 CPU屏幕
- **MCU接口概述**

80并口全称为8080并口协议是有因特尔公司提出的，除8080接口协议外还有摩托罗拉的6800接口协议.

- **MCU接口组成**

- ![](https://i.imgur.com/V989Fl9.png)

这类模式通常有下列接口信号:
/RES（复位线），
DB0~DB17(双向数据线),Ｄ/Ｉ（数据/指令选择线，1:数据读写,0:命令读写），CS（片选信号线，如果有多片组合,可有多条片选信号线）,/WR（MPU向LCD写入数据控制线）,/RD（MPU从LCD读入数据控制线)；
CPU接口的LCD driver IC主要功能是对host端发送过来的数据/命令，进行处理，并最终在LCD屏幕上显示出来。整个过程不需要同步信号和时钟的参与，内部一般集成的的有ram,所以一般很难做大分辨率的显示。


CS连线为片选信号线，RS寄存器选择信号线，RD读信号线，WD写信号线。这些连线都属于控制信号线。
另外还有0到18根的双向数据传输线，每一根线可以传输一个bit，8080总线可以支持：6bit 8bit 9bit 
16bit 18bit。**目前接触到的是16/8bit (RGB 565)和18/9bit (RGB 666),也就是16线和18线的data线，比如有些LCD driverIC支持硬件上支持最多16线，但是实际上需要看host和LCD device 间的连线是多少根，如果是8根的话，host端就要配置成8bit(RGB565)格式输出，LCD device端也需要有电路来选择接受8bit(RGB565)的格式输入。**
 
- **MCU接口时序**
![](https://i.imgur.com/t3eDDs1.png)
上面是通用的CPU屏接口时序图，对于8080 这种低速的串行总线，在调试的时候可以使用数字逻辑分析仪，抓取host端发送出来的数据，下面使用一张数字逻辑分析仪抓取到的时序来说明一下整个8080 接口的工作过程：
![](https://i.imgur.com/Ld7U2lj.png)
上图是host项devic lcd传输一个bit数据的过程：
**首先，CS片选信号拉低，告诉device lcd准备要接收数据；**
**之后RS信号拉高或者拉低，拉低表示此次数据的传送类型是寄存器地址，拉高表示此次数据传输类型是参数，一般的顺序是先传送地址，然后依次传送0到若干个参数，地址和参数一般都是32bit为单位传送的，一般host端的控制器都会提供一个32bit的寄存器，用来写入要传送的数据**
**WD信号用于数据采样，在上升沿或者下降沿采样数据**
**data 0 信号线上发送数字信号，高电平代表1，低电平代表0，比如传送8bit的数据，就需要8根数据线来传送，分析仪这里值抓取了data0上的信号，也就是bit0的数据。**
下图是一张完整的初始化命令发送时序图：
![](https://i.imgur.com/yp7MOQ3.png)
MCU接口刷屏：host端需要软件控制一帧数据的刷新，因为MCU接口host控制器不会项rgb控制器那样产生周期为60Hz的vsync同步信号，所以当fb里面有新的数据更新到host控制器时候，需要软件控制刷新一次数据给device lcd端，通常是操作寄存器的一个start bit位。


### 3.0.3 MIPI-DSI

- **DSI接口概述：**

首先，来了解一下MIPI，MIPI（移动行业处理接口）是Mobile Industry Processor Interface的缩写。
MIPI联盟是一个开放的会员制组织。 2003年7月，由美国德州仪器（TI）、 意法半导体（ST）、 英国ARM和芬兰诺基亚（Nokia）4家公司共同成立。

MIPI联盟旨在推进手机应用处理器接口的标准化，在这样的背景下MIPI DSI规范孕育而生。

DSI，Display Serial Interface(串行显示接口)，定义了一个位于处理器和显示模组之间的高速串行接口。

- **DSI接口组成：**

**Host Device端和Peripheral端：** Host Device端就是通常意义上的LCD控制器，集成在SOC上，由处理器芯片厂商设计实现；peripheral端就是外接panel的driver IC，由panel driver IC的设计商设计实现，都遵循MIPI-DSI的标准，如下图：

![](http://i.imgur.com/9RWs4Fe.png)

**1. 接口Lane的类型：**

1对单向传输差分时钟线。

1～4对单向传输差分数据线，其中data0支持双向传输，用于传输控制信号。

**2. 接口Lane状态和电压**

• Lane状态 

    低功耗（Low-Power）信号模式（用于双向传输控制信号）：10MHz (max)
	高速（High-Speed）信号模式（用于高速数据传输，数据传输采用DDR方式，即在时钟的上下沿都有时间传输）：80Mbps ~ 1Gbps/Lane
  
• Lane电压 
    
	LP：0-1.2V
    HS：100-300mV (200mV)


在实际应用中，可以直接测量差分线上的电压值，这样可以初步确定lane当前的工作模式。

**3. DSI接口的工作模式**
   
**Command 模式：**
类似于MPU接口，peripheral端需要集成内部ram，用来接收Host端发送的命令和显示数据，peripheral端内部的driver IC 负责将ram中的显示数据更新到显示屏上显示出来。

**Video模式：**
类似于RGB接口，Host 端到peripheral端采用实时的像素数据流，这种模式必须用**高速模式传输数据**，像素数据会被直接更新到显示屏上显示出来。video 模式下的三种传输数据包序列：

* Non-Burst Mode with Sync Pulses
* Non-Burst Mode with Sync Events
* Burst Mode

![](http://i.imgur.com/48EwqXc.png)

**HSA，HBP，HFP，BLLP**：是Blanking Packet用长包（0x19）发送，没有数据。

**VSS、VSE**：Sync Event Packet,使用长包发送。

**HSS、HSE**：Sync Event Packet，使用长包发送。

**RGB**：Packet，使用长包发送。

**注意：** 根据mipi-dsi的video mod三种时序图，HBP、HFP只存在有效行的消隐期内，在帧消隐期内则不存在。

**a) Non-Burst Mode with Sync Pulses**

![](http://i.imgur.com/S7UDzB0.png)


**b) Non-Burst Mode with Sync Events**

![](http://i.imgur.com/rzb5QXm.png)

**c) Burst Mode**

![](http://i.imgur.com/RcBaEek.png)



在实际应用中，DSI 控制器被设计成video mode 和command mode可以任意切换，LCD 模组端支持其中一种或者两种。DSI 控制器在Video Mode操作模式下必须使用高速的传输像素数据，Command Mode操作模式没有规定使用High-Speed或Low Power的传输模式，即使外部LCD模组为Video Mode，但通常在LCD模组初始化时还是使用Command Mode模式来发送命令读写寄存器，因为在低速下数据不容易出错并且容易抓信号。Video Mode当然也可以用High-Speed的方式来发送命令，Command Mode操作模式也可以使用High-Speed。

**4. DSI Packet**

DSI 有两种包结构：**Long packet和short packet**。

![](http://i.imgur.com/iRxkB1z.png)

**a) short packet结构**

  	• 包头部（4个字节）
    
		• 数据标识(DI) 1个字节
    
		• 包数据- 2个字节 （长度固定为2个字节）
    
		• 错误检测(ECC) 1个字节
  	
	包大小：长度固定为4个字节

![](http://i.imgur.com/Gre8yhD.png)


**b) long packet 结构**
  
	• 包头部（4个字节）
    	• 数据标识(DI) 1个字节
    	• 数据计数- 2个字节 （数据填充的个数）
    	• 错误检测(ECC) 1个字节
  
	• 数据填充(0~65535 字节)
    	• 长度 = WC*字节
  	
	• 包尾：校验和（2个字节）
  	
	• 包大小： 4 + (0~65535) + 2 = 6 ~ 65541 字节

**c) packet 数据类型**

![](http://i.imgur.com/y1le9lM.png)

- **DSI接口时序：**

Host和peripheral之间的接口时序，DSI不像RGB、LVDS那样有Sync模式和DE模式之分，它有的是video模式和command的区别，为了统一分析LCD之间的接口时序，这里也套用Sync模式和DE模式，进行DSI接口时序的分析说明：

**a)** SYNC模式下的HSYNC和VSYNC信号的时序图，红色框图内为消隐期时间：

**行消隐期时间 = Twh + Thbp + Thfp**

![](http://i.imgur.com/gXy5QGV.png)

**帧消隐期时间 = Twv + Tvbp + Tvfp**

![](http://i.imgur.com/8IaOiog.png)


**b)** DE模式时序图，红色框图内为消隐期时间：

**行消隐期时间 = Thp**

![](http://i.imgur.com/yoRrPpB.png)

**帧消隐期时间 = Tvp**

![](http://i.imgur.com/H3tWKSm.png)

我们知道DSI接口是串行接口，在硬件链接上，时序相关的只有4组数据引脚和1组时钟引脚，没有提供Hsync、Vsync或者data enable引脚来做信号同步，在DSI的接口信号里，通常意义上的Hsync、Vsync等的同步信号会被打包起来，以长包或者短包的形式和显示数据组织在一起串行的发送出去。具体的组织形式就是DSI video模式下的三种序列。


- **DSI Host端时钟**

	根据屏spec中提供的下面信息**帧结构**、**传输时序**、**传输包格式**可以较精确的算出**每条lane上的实际传输速率(bit per second)**，这样可以很方便的配置DSI host端需要的工作时钟。

![](http://i.imgur.com/SVajPla.png)

![](http://i.imgur.com/xIya2Gp.png)

![](http://i.imgur.com/PsyeOnm.png)

下面是每条lane bps计算步骤的详细描述：

	1.	HSS是一个短包，只有4Byte
	2.	在Event模式，HBP就要包括Hsync+HBP=16Dots+32Dots=48Dots=48x3=144 Byte,但HBP要用长包发送，需加6Byte的包头包尾，共144+6=150 Bytes
	3.	RGB数据一行为1920 Dots=1920x3=5760 Bytes，要加6Byte包头包尾，共5766 Bytes
	4.	HFP为112Dots=112x3=336Bytes,用长包发，要加6Byte包头包尾，共340Bytes。
	5.	那么所有一条Line加起来为：4+150+5766+340=6260 Bytes，共6260x8=50080 Bits.
	6.	那么一帧有加上消隐期共1235行，按60Hz算，一秒钟有1235x60=74100行
	7.	那么一秒钟要传送多少Bit呢？74100行x 50080 Bits=3.7GBit
	8.	因为有4条Data Line，那么每条Lane的BitRate为3.7G/4=927Mbps。

	9.	那么PHY Clock需要多少呢？由于是双沿传送，需要的时钟为927M/2=463M，这就是DSI_HSCLK，是串行传输时钟，UI=1/927M=1.08ns

 	10.	那么并行时钟DSI_CLK就是463M/4=116M(因为并行时钟是单沿，串行式中是双沿，所分频为4，并转串前用)，那么Tdsi_clk=4.3ns
	11.	DSE_CLK就是跟DE接口的时钟，直接2080x1235x60=154M，只要比该值大就可以
	12.	其余参数只需要通过UI或Tdsi_clk配置即可

**有关MIPI-DSI帧率（每秒X帧）、帧结构、pixe clk关系：**

根据帧结构（hbp + hsw + xres + hfp）* (vbp + vsw + yres + vfp)计算出每一帧需要传输的像素点数dot。然后乘以帧率，就是在当前帧率下每秒需要传输的总的像素数据量dot。根据每秒传输的像素数据量可计算出dsi-phy-clk的值，分频后得出pixe\_clk的值，pixel\_clk的值是从spec中得到，然后我们期望系统分出来可以使屏正常工作的值。

理论上通过此方法只需要在dts中配置好帧结构timgings值和帧率，就可以获得实际上想要的帧率输出，但实际上需要根据屏原厂给出的spec选择帧率的输出。

有关dsi Host、DE、dsi peripheral模块之间的工作时钟关系：

![](http://i.imgur.com/5rcre3I.png)

dsi 工作时钟计算注意事项：
![](http://i.imgur.com/E2ogEXy.png)

- **DSI DCS命令**

DSC： set\_address\_mode（36h）

功能： panel内部driver IC控制屏幕旋转方向。


mipi LCD的driver IC一般都支持，vertical flip和horizon flip。

首先确定LCD的driver IC默认的扫描方向，以竖屏为例

![](https://i.imgur.com/QAqpdhO.jpg)

	SC: start Column(列)
	EC:	end Column（列）

	SP:	start page
	EP:	end page

上图竖屏默认是从左上角开始横向刷屏的。

通过mipi DCS命令可以改变刷屏方式

![](https://i.imgur.com/00UyMQ3.png)

bit7和bit6两个位控制纵向和横向的刷屏方向

![](https://i.imgur.com/zLwCyUz.png)

bit1和bit0两位需要与bit7和bit6位配合起来使用，共同完成flip
![](https://i.imgur.com/lNDAUjH.png)

	参数的含义：
	no flip                                         0x00
	vertical flip                                   0x81
	horizontal flip                              	0x42
	rotate 180(vertical flip + horizontal flip)     0xc3


### 3.0.4 EDP

#### EDP 调屏
**现象一：** 屏幕画面偏暗，调节背光，亮暗效果无法改善。

**可能原因：**EDP控制器输出的数据宽度与EDP屏要求的数据宽度。

## 3.1 TV 
### 3.1.0 HDMI
- **HDMI接口概述**

高清晰度多媒体接口（High Definition Multimedia Interface，HDMI）是一种数字化视频/音频接口技术，主要用于传输高清音视频信号，最高数据传输速度为18Gbps（2.0版）。
由日立、松下、飞利浦、Silicon Image、索尼、汤姆逊、东芝七家公司在2002年发布了HDMI 1.0版标准，标志着HDMI技术正式进入历史舞台。	

- **HDMI接口组成**

	1. **HDMI引脚**

HDMI有A、B、C、D、E五中引脚类型，比较常见的是Type A:


![](http://i.imgur.com/m9CTsKT.png)

	1-9 都是TMDS（Transition Minimized Differential Signaling 最小化传输差分信号）数据传输实际上用到的引脚，分为0,1,2三组。
    10-12 为TMDS时钟信号。TMDS clock就像是对像素的打包，在一个clock周期内分别在三个Channel传输一个像素的R、G、B（8bit）信号。
    13 为CEC（consumer electronic control）类似一种扩展的HDMI功能，供厂家自己定制HDMI消息，（比如说你有一台小米的电视盒子与TV，两者用HDMI线接上，如果你用TV的遥控器可以控制小米电视盒子，让小米电视盒子执行某种功能，那么该功能的命令信号就是通过TV与电视盒子之间间的CEC引脚传输的）。
    14 为保留引脚，未使用（或者也可以为CEC提供多一个引脚）。
    15-16 为I2C引脚，用于DDC（Display Data Channel，主要用于EDID与HDCP的传输）传输，具体可以查看。在HDMI的流程中，DDC通信几乎是最先做的（前有Hotplug），因为HDMI的主从两个设备需要通过DDC来获得他们对方设备的EDID，从而得到各种信息，并且通过比较timming以确定以后送出来的timming为最合适的。
    17 为接地引脚。
    18 为5v的AC（alternating current，交流电）引脚。
    19 为Hotplug（热拔插）引脚（用于监测HDMI设备有没有存在，如果存在（Hotplug为high）那么可以通过DDC去读EDID）。

HDMI热插拔检测机制：
	
	当source device通过hdmi线与显示器相连接时，source device通过hdmi的第18引脚将+5v电压加到显示器的DDC存储器（EDID数据存储器）向DDC存储器供电，确保即使显示器不开机，source device也能通过HDMI接口读取EDID的数据。
	source device开机后产生5v_sys并通过第18脚向显示器供电，此时显示器接收到5V电压后通过内部电路使HDMI接口第19引脚HPD转变为高电平，source device的HDMI控制器检查到HPD为高电平时，断定为显示器通过HDMI与主机相连，并通过HDMI接口的第15、16脚DDC通道（I2C）读取显示器中的EDID数据，并使source device中的TMDS信号发送，HDMI控制器开始工作。
	当显示器与sourece device之间的HDMI连接断开时，source device一侧的HPD信号为低电平，source device的HDMI控制器disable输出。

HDMI EDID信息：

	一般电视端的edid主要包含block0和扩展block块儿两部分，每部分128 bytes。
	i2c在读取edid数据时候，是按照块（128 byte）为单位读取的。
	读取之前i2c adapter(hdmi controller)需要将电视端存储块数据的地址（memory address within chip）通过i2c发送给i2c client(TV),之后完成edid数据读取操作

通过I2C操作获取EDID信息流程：
![](https://i.imgur.com/MrB2SU8.png)

第一步，段选segment：

**DDC Address 60H：** DDC设备的从设备地址，用于和其他挂接在I2C总线上的从设备区分开来。

此地址用来做段选，段选命令（0x60 00）发送出去电视端成功相应之后会相应一个ACK，在segment0段选的时候不要care发送0x60以及段指针0x00时候的ack位，原因是有部分电视不支持segment读取方式。

注：第一步主要是为了判断段选是否可以成功，第二步是正真读取edid信息的操作。

![](https://i.imgur.com/NZe7y9b.png)
第二步，读取segment段内的edid信息：

**DDC EDID Address A0H:** edid设备的从设备地址，真正可以读取到edid信息的从设备地址，在这个从设备中存储edid信息的寄存器地址范围为0-ffH

总结：

首先发送segment0的段选命令 0x6000, 不用关心NACK，然后发送segment0的edid信息读取命令。segment0一共有256byte的信息，共分为两个大小为128byte的block。一般情况是按照block读取block的内容, block 0的寄存器机制为0x0, 内容长度为128byte，block 1寄存器的地址为0x80,内容长度为128byte。比如，读取block 0的命令为 0xa0 00，block 1的命令为0xa0 80（或者也可以自定义读取的地址，范围为0x0~0ff）。segment 1的信息读取方式类似，但是需要关心发送命令0x6001的ACK是否成功。


注意：

1）	在segment0读取的时候不要care发送0x60以及段指针0时候的ack位，原因是有部分电视不支持segment读取方式。

2）	在segment0读取分成两个128byte去读，原因是有的可能只支持block0（byte127=0）。读取完判断byte127的bit0是否为1，为1再按照上面的时序读取后面的128byte。

3）	在判断byte127的bit1是否为1，为1则表示其支持segment1，然后依照上面的方式去读，但和segment0不同的是此时在发送0x60和段指针1的时候需要用ack来接收，以防有的byte127的bit1出错成了1，而实际上并不支持segment1。读取segment1可以一次性读取256byte。

下图是一段过HDMI认证获从目标端读取取到的4个block edid信息

![](https://i.imgur.com/Oye3tCv.png)


- **HDMI接口传输**

**1. 传输类型**

HDMI TMDS传输的数据类型有三种（加上Hsync与Vsync就算4种）：

    Preamble（控制信息），主要用于控制接下来传输的数据是Data Island或者Video Data。
    Data Island（数据包），各种类型的包信息，包括音频数据包，图像信息包等。
    Video Data （视频信息），视频像素数据，HDMI可以传输RGB与YUV两种格式的像素数据。
    还有Hsync与Vsync。

HDMI的数据传输有TMDS0，TMDS1，TMDS2三个通道，每个通道的传输流程都是一样的：

![](http://i.imgur.com/uzbirDm.png)
如果是8bit的数据进入TMDS编码器，得到抗干扰性强的10bit TMDS信号，然后再进行串行化输出；在接收端收到串行的HDMI信号后，进行信号复原，得到10bit的TMDS信号，最后用TMDS解码器解码得到原来的8bit数据。

总体传输流程如下：

![](http://i.imgur.com/4LOXpbK.png)

![](http://i.imgur.com/zhjsyG8.png)
	
	1. 如果传输的是Video Data，并且格式为RGB，那么会占用三个通道的所有24bit输入，Channel0[7:0]用于传输B，Channel1[7:0]用于传输G，Channel2[7:0]用于传输R。
   	
	2. 如果传输的是Data Island，则占用三个通道共10bit输入，Channel0[3:2]用于传输Data Island Header（包头），Channel1[0:3]与Channel2[0:3]用于传输Data Island Content（包内数据）。
    
	3. 如果传输的是Preamble，则占用1，2两个通道共4bit输入，Channel1[1:0]与Channel2[1:0]分别为CTL0,CTL1,CTL2,CTL3，用于判断接下来输入的是Video Data或者Data Island

	4. 对于Hsync与VSync，会占用Channel0通道的两个bit输入，Channel0[0]为Hsync，Channel0[1]为Vsync

**2. 传输周期**

HDMI的TMDS数据传输可以分为三个传输周期：

![](http://i.imgur.com/FqDcFYE.png)


    Control Period期间会传输Hsync，Vsync，并且在该时期的最后阶段会传输Preamble
    Data Island Period期间会传输Data Island（数据包），也会有Hsync与Vsync
    Video Data Period期间会传输Video Data（视频像素数据）

某帧的总体周期如下：
![](http://i.imgur.com/BMxB6uO.png)

三个传输周期的过渡如下：

![](http://i.imgur.com/OkPxWCn.png)


    左边是Control Period，传输有Hsync，Vsync与Preamble
    中间是Data Island Period，传输有Hsync，Vsync，以及两个Packet Header与Packet（每32个clock 一个packet）；另外Data Island的两端会用Guard Band保护并隔开Data Island的数据，因为这个阶段传输的数据大多是非常重要的，比如其中就有图像分辨率，决定后面的Video Data数据的显示方式
    右边是Video Data Island，传输视频像素数据，在该时期的开头也有Guard Band


**3. HDMI支持的视频显示格式**

首先计算一下HDMI接口提供的数据率：

    HDMI1.3版本以前，时钟为165MHz，支持24色深，3个数据通道1个时钟通道，每通道传输10bit（8位颜色数据、2位控制数据）。则一个HDMI连接的数据率是：165MHz×10bit×3=4950Mbit=4.95Gbps（令进制约等于1000），此外再加上控制数据，

    从HDMI1.3版本开始，时钟提高到340MHz，支持24bit/30bit/36bit/48bit的色深。假设采用24bit色深，每通道传输10bit。则一个HDMI连接的数据率为340MHz×10bit×3=10200Mbps=10.2Gps。如果采用30bit色深，则数据率为12.24Gbps。如果采用36bit色深，则数据率为14.28Gbps。如果采用48bit色深，则数据率为18.36Gbps。一般情况下，30bit色深已经达到人类眼睛所能分辨的色彩的极限，36bit/48bit色深只是具有象征意义。不过需要注意的是，不同比特的色深只是通过增加传输的数据量来提供更加丰富的色彩，如果用像素点来表示的话，仍然是1秒钟传输340M个像素所需的数据量。

    HDMI的显示格式：

    在HDTV中规定了三种标准：720P、1080i和1080P，帧（场）频为60Hz。这里以1080P/60Hz为例。每帧像素数为1920×1080=2073600（约为2.074M）个。刷新率为60Hz，则每秒需要信息的像素次数为2073600×60=124416000（约为124.42M）个，需要的数据量为124416000×10bit×3=3732480000(约为3.74Gbit)。由前面的计算可知，HDMI最初标准的数据率可以达到4.95Gbps，所以用起来绰绰有余。

    对于3D-TV,数据率要加倍，理论上需要达到至少3732480000×2=7464960000bit（约为7.46Gbps）。所以数据率最低为10.2Gbps的HDMI1.3版本可以支持3D。新面世的HDMI1.4a中提出了支持3D的方案。


### 3.1.1 CVBS 

