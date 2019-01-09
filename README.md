# documents
写作管理库

MarkDown格式

Typora编辑器

assets文件夹用于保存图片

---

0.  pl led
1.  ps led
2.  ps uart
3.  ps emio，开关PL端8个LED，5个按钮，试验接收中断
4.  axi gpio，开关PL端8个LED，5个按钮，试验中断
5.  axi bram
6.  ps读写DDR
7.  pl通过axi_lite与ps通信：参考米联==s02_ch12==
    1.  自定义axi_lite接口模块，2个方向
    2.  PS的M_AXI_HPM0_FPD连接axi_lite的slave，由PS读写PL的寄存器
    3.  PS的S_AXI_HP0_FPD(需要在地址配置时与DDR连接)连接axi_lite的master，由PL读写PS端的DDR
8.  pl读写ps端DDR：==参考米联s03_ch01~ch02==
    1.  使用axi_dma和AXI4_STREAM_DATA FIFO
9.  pl读写ps端DDR：axi_full
    1.  直接建立master连接ps的S_AXI_HP0_FPD(需要在地址配置时与DDR连接)
10.  中断：pl，定时器，串口，gpio
11.  VDMA实现图像传输
12.  linux：QT，OpenCV
13.  程序加载：SD卡，flash，USB
14.  终级试验：播放SD卡的电影，在pl进行图像处理后显示