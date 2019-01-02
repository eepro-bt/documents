# AXI总线类型

-   AXI_LITE
-   AXI
-   AXI_STREAM

AXI总线协议分为主master和从slave两端。

master与slave之间由AXI Interconnect连接，作用是将1个或多个AXI主设备与1个或多个AXI从设备相连接。



# axi lite时序

## 写操作

1.  master：同时设置awvalid和wvalid有效
2.  slave：等待awvalid和wvalid同时有效，同时将awready和wready设置1
3.  slave：在awvalid awready wvalid wready同时为1的情况下，bvalid设1，同时取出awaddr和wdata
4.  master：收到awready将awvalid设0，收到wready将wvalid设0
5.  master：收到bvalid将bready设1，下个时钟周期设0
6.  slave：收到bready将bvalid设0

## 读操作

1.  master：设置arvalidy为1
2.  slave：收到arvalid将arready设1，下个时钟周期设0
3.  slave：在arready arvalid同时为1的情况下，rvalid设1，axi_rresp设0有效
4.  master：收到arready将arvalid设0
5.  master：收到rvalid将rready设1，下个时钟周期设0
6.  slave：收到rready将rvalid设1
7.  master：在rready rvalid同时为1的情况下，检查rdata数值



# axi stream时序

数据从master流至slave，单向流动

master端tdata tstrb tuser tlast全部与tvalid同步，tlast与tvalid的最后1个时钟周期对齐

slave端收到tvalid后设为1，保持的长度必须与tvalid一致



# axi_lite用于寄存器交互

自定义axi_lite接口IP核，修改代码后，可以由PS向PL直接写寄存器

与M_AXI_HPM0_FPD接受PS的控制，或者与S_AXI_HP1_FPD连接控制PS

参考**米联s02_ch12**

说明AXI三种总线时序，单独文档



# PS读写DDR

建好后，在mem_demo_bsp->ps7->cortexa9_0的路径下，打开xparameters_ps.h这个头文件，这个头文件是cortexA9可以直接控制的外设地址的宏定义。在里面可以找到DDR的地址，可以看到如下代码： 

/* Canonical definitions for DDR MEMORY */ 
#define XPAR_DDR_MEM_BASEADDR 0x00000000U 

#define XPAR_DDR_MEM_HIGHADDR 0x3FFFFFFFU
--------------------- 

在mem_demo_bsp->ps7->cortexa9_0的路径下，打开xil_io.h这个头文件，这个头文件是cortexA9可以直接控制的内存映射或者映射到了地址空间的IO。在里面可以看到如下代码： 

//从某个地址读数据 
u8 Xil_In8(INTPTR Addr); 
u16 Xil_In16(INTPTR Addr); 
u32 Xil_In32(INTPTR Addr);

//向某个地址写数据。 
void Xil_Out8(INTPTR Addr, u8 Value); 
void Xil_Out16(INTPTR Addr, u16 Value); 

void Xil_Out32(INTPTR Addr, u32 Value)
--------------------- 

在xil_mem.h中的Xil_MemCpy用于内存考贝



block design中的address Editor指明各接口对应的外设base

PL读写PS端DDR

# axi dma

stream通过dma进行地址映射的传输

axi_dma中axi_lite用于ps对dma的control，而mm2s和s2mm用于数据传输

axi_dma接口猜测

读接口

-   M_AXI_MM2S用于连接mm的slave接口，用于发送读指令

-   M_AXIS_MM2S用于将读出的数据以master stream的形式送出，用于取出读到的数据

写接口

-   S_AXIS_S2MM用于写数据进入
-   M_AXI_S2MM用于连接mm的slave接口

两种模式

direct register

scatter gather

S_AXI_LITE接口用于读写功能寄存器，用于启动读写指令与地址，如果S_AXI_LITE与ps连接，则由ps通过功能寄存器控制DMA收发

用control register启动dma操作

启动读写都是异步操作，用mm2s_introut和s2mm_introut中断作为读写完成指示

合用xaxidma.c和.h控制dma操作，配置见xaxidma_hw.h

**数据放在axis_data_fifo中**

dma如果需要数据缓冲，最好配合AXI4_STREAM_DATA FIFO，与普通fifo一样使用，只是数据写入fifo是由外部master控制，==数据读出也由外部slave控制==

==当fifo内有数据可以读出时，M_AXIS的tvalid就会变成高有效，等待外部slave送入的ready即可以完成一次读出==

如果自行缓冲，可以使用米联s02_ch12的自定义IP方法建立对应的axi接口，而用fifo保存原始数据



# 为什么axi_lite连接失败？

很可能是由于SMMU进行了虚拟地址转换

在ug1085，p369

System Memory Management Uint

to offer isolation and memory protection, it restricts device access for dma capable io to a pre-assigned physical space