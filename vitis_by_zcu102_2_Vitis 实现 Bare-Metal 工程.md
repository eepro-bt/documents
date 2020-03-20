具体方法与 SDK 的 Bare-metal 工程基本一致，详细操作可以参考 [zcu102 系列文档]()。

## 建立 Vivado 工程

建立基于 zcu102 开发板的 Vivado 工程。

建立 Block Design，添加 Zynq UltraScale+ MPSoc 的 IP

 ![image-20200115092917242](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200115092917242.png)

点击 Run Block Automation，自动配置 IP 模块。

双击打开配置窗口查看 UART 0 和 UART 1 已按照 zcu102 开发板的硬件连接设置完成。

![image-20200115093227375](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200115093227375.png)

并且在 PS-PL Configuration 页关闭 AXI HPM0 FPD 和 AXI HPM1 FPD

![image-20200115093557195](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200115093557195.png)

保存 Block Design，Generate Output Products 并且 Create HDL Wrapper，完成后 Sources 窗口如下：

![image-20200115094207894](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200115094207894.png)

在 Flow Navigator 中选择 Generate Bitstream，等待运行结束。

在 Vivado 菜单栏选择 File > Export > Export Hardware，选中 Include Bitstream，点击 OK 按钮。

![image-20200115132948002](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200115132948002.png)

导出完成后在 Export to 路径下出现导出的 xsa 文件：

![image-20200115133659435](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200115133659435.png)

## 建立 Platform Project

启动 Vitis Software Platform，设置 Workspace 路径后点击 Launch 按钮。

![image-20200109072120445](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200109072120445-1579066313985.png)

进入 IDE 后在菜单栏选择 File > New > Platform Project

设置 Project name 后点击 Next 按钮

![image-20200109072608859](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200109072608859-1579066313986.png)

选中 Create from hardware specification (XSA) 后，点击 Next 按钮

![image-20200109072714562](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200109072714562-1579066345973.png)

点击 Browse 按钮，选中由 Vivado 工程导出的 xsa 文件

选择 Operating system 为 standlone

选择 Processor 为 psu_cortexa53_0

点击 Finish 按钮

![image-20200115133813292](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200115133813292.png)

关闭默认的 Welcome 界面，在 Explorer 中的工程上右键点击，打开菜单选择 Build Project

![image-20200115134130829](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200115134130829.png)

## 建立 Application Project

在菜单栏选择 File > New > Application Project

弹出窗口中设置工程名称，然后点击 Next 按钮

![image-20200109075511983](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200109075511983-1579067746278.png)

选中之前建立的 Platform Project，然后点击 Next 按钮

![image-20200115135645854](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200115135645854.png)

接下来的页面保持默认，点击 Next 按钮

![image-20200109075729098](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200109075729098-1579067746279.png)

选中 Hello World 模板后点击 Finish 按钮

![image-20200109075808052](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200109075808052-1579067746279.png)

在 Explorer 中出现 hello 工程，双击打开 helloworld.c

![image-20200115135821935](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200115135821935.png)

修改原始代码为下方代码

```c
#include <stdio.h>
#include "platform.h"
#include "xil_printf.h"


int main()
{
    init_platform();

    print("Hello Uart\n\r");

    cleanup_platform();
    return 0;
}
```

## 运行测试

双击打开 Platform Project 中的 platform.spr

![image-20200115140038838](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200115140038838.png)

选中当前 a53 的 Board Support Package，在页面内点击 Modify BSP Settings... 按钮

![image-20200115140133244](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200115140133244.png)

在弹出配置窗口的 standalone 页面选择 stdout 为 psu_uart_0，或者 psu_uart_1

![image-20200115140326866](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200115140326866.png)

重新 Build Platform Project

在 Application Project 的右键菜单选择 Build Project

板卡上电并且打开 TinySerial，打开 ttyUSB0 串口

![image-20200115140639961](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200115140639961.png)

在 Application Project 的右键菜单选择 Run As > Launch on Hardware

在 TinySerial 中收到 Hello Uart

![image-20200115140816369](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200115140816369.png)

同样的方法测试 uart_1。

在 BSP Settings 中选中 stdout 为 psu_uart_1，在 TinySerial 中打开 ttyUSB1 串口，却发现串口打开失败，于是用查看串口状态

![image-20200115142928843](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200115142928843.png)

原来是 ttyUSB1 权限设置问题，于是修改权限：

![image-20200115143301942](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200115143301942.png)

TinySerial 中打开成功后运行 Vitis Application

![image-20200115143449670](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200115143449670.png)

发现未收到 FSBL 的信息，原因是 FSBL 的 BSP 设置为 uart_0 的 stdout，使用 2 个 TinySerial 程序分别打开 ttyUSB0 和 ttyUSB1，再次运行 Vitis Application：

![image-20200115143804628](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200115143804628.png)

## Vitis 自带的串口 Terminal

在 Vitis 菜单中选择 Window > Show View > Xilinx/Vitis Serial Terminal

在界面上点击 + 号按钮连接串口（旁边的 x 按钮用于关闭当前串口）

![image-20200115144144301](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200115144144301.png)

在弹出窗口中配置串口

![image-20200115144208589](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200115144208589.png)

按前述办法运行 Vitis Application，Vitis Serial Terminal 收到正确的数据

![image-20200115144325869](vitis_by_zcu102_2_Vitis%20%E5%AE%9E%E7%8E%B0%20Bare-Metal%20%E5%B7%A5%E7%A8%8B.assets/image-20200115144325869.png)