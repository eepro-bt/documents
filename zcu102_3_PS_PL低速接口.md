# EMIO

MIO接口由PS直接与外设连接，不需要在Vivado工程中设置管脚约束，不用加载bit文件即可以使用PS程序

EMIO可由让PS通过该接口控制与PL端连接的GPIO，需要在Vivado工程中设置管脚约束，需要加载bit文件后才能使用PS程序

试验与板上5按钮和板上8 LED的连接

见Miz702文档

==直接入sources将verilog模块拖入block design，即可自建RTL模块==或者空白处右键选择add module

# AXI

参考ug1037

-   axi：高性能memory-mapped数据连接，支持批量传输
-   axi-lite：简单低速的memory-mapped数据连接，不支持批量传输，一次只能一个字32bit
-   axi-stream：高速流数据，无地址映射，需要给过axi_dma进行地址映射

通过AXI接口相关IP与AXI接口连接，在IP Catalog的AXI Infrastructure中

-   axi_gpio：通过PS端访问与PL连接的GPIO外设
-   axi_bram：
-   axi_timer
-   axi_stream_fifo：ps与pl互传数据
-   axi_data_fifo：
-   axi_register_slice
-   axi central dma用于在两个地址映射的master和slave间传输

