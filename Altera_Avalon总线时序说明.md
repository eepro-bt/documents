Avalon总线是Altera FPGA使用的通用总线，相当于在Xilinx FPGA中使用的AXI总线（Avalon总线也可以在Platfrom Designer中与AXI总线连接并转换）

Avalon总线的官方说明文档为[Avalon Interface Specifications](https://www.intel.com/content/dam/www/programmable/us/en/pdfs/literature/manual/mnl_avalon_spec.pdf?wapkw=avalon+interface&_ga=2.175066085.678843979.1550213148-1230719698.1545026297)，本文主要参考该文档的18.1版本

Avalon总线分为3种类型：

-   Memory-Mapped：
-   Streaming：
-   Tristate Conduit：

# Avalon Memory-Mapped

# Avalon Streaming

# Avalon Tristate Conduit

# 自定义Avalon总线模块

在Quartus中打开Platform Designer，在File菜单中选择New Component

在Signals & Interfaces页添加接口，之后在Files点击Create Synthesis File from Signals



**在Quartus 17.1中，必须先保存Platform Designer中的Component，关闭edit后，再次打开edit，才能在Files页选择Create HDL Template**

否则保存的顶层HDL文件中，模块名称只能为new component，不能修改