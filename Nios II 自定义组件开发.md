软件环境：Quartus Prime Standard 18.1 Window 10

硬件环境：小梅哥 AC501 开发板

主要参考：

- [Intel Quartus Prime Standard Edition User Guide/Platform Designer](https://www.intel.com/content/dam/www/programmable/us/en/pdfs/literature/ug/ug-qps-platform-designer.pdf)
- [Avalon Interface Specifications](https://www.intel.com/content/dam/www/programmable/us/en/pdfs/literature/manual/mnl_avalon_spec.pdf)

本实验使用 Nios 的自定义组件控制开发板上 LED 闪烁。

本文首先建立了基于片上 RAM 的基础 Nios 工程，之后在该工程上添加基于 Avalon-MM 接口的自定义组件，修改 Nios 软件后实现 LED 闪烁。

#   建立 Quartus 工程

打开 Quartus 软件，选择菜单 File > New ...，在弹出窗口中选择 New Quartus Prime Project，点击 OK 按钮。

![1586480681785](Nios II 自定义组件开发.assets/1586480681785.png)

在 New Project Wizard 对话框中，按提示选择 Next 页面，需要进行特殊设置的页面包括以下页面，其它页面保持默认。

设置工程名称和路径：

![1586480832498](Nios II 自定义组件开发.assets/1586480832498.png)

选择 FPGA 芯片型号：

![1586481007915](Nios II 自定义组件开发.assets/1586481007915.png)

完成全部配置后点击 Finish 按钮创建新工程 cus_com。

在 Quartus 选择菜单项 Assignments > Settings，在配置窗口选择 Compilation Process Settings 页，选择 Use all available processors：

![1586481362595](Nios II 自定义组件开发.assets/1586481362595.png)

# 用 Platform Designer 建立 qsys 模块

在 Quartus 选择菜单项 Tools > Platform Designer，打开 Platform Designer 界面：

![1586481593892](Nios II 自定义组件开发.assets/1586481593892.png)

双击 clk_0 模块，配置时钟频率为 50 MHz

![1586481663299](Nios II 自定义组件开发.assets/1586481663299.png)

通过 IP Catalog 界面添加 System ID 模块，保持默认配置点击 Finish 按钮：

![1586481835711](Nios II 自定义组件开发.assets/1586481835711.png)

添加 JTAG UART 模块，保持默认配置点击 Finish 按钮：

![1586481963397](Nios II 自定义组件开发.assets/1586481963397.png)

添加 On-Chip Memory 模块，并配置为 128 KB 大小（小于当前 FPGA 芯片 1400 Kb 的 RAM 空间总量），其它配置项保持默认，点击 Finish 按钮：

![1586482203786](Nios II 自定义组件开发.assets/1586482203786.png)

添加 Nios II Processor 模块，设置为 Nios II/e 模式，其它配置保持默认，点击 Finish 按钮：

![1586483056612](Nios II 自定义组件开发.assets/1586483056612.png)

按下图，在 System Contents 界面连接各模块接口，保存当前设计，选择菜单项 System > Assign Base Addresses：

注意将 nios2 模块的 instruction_master 连接至 onchip_memory 的 s1：

![1586495564767](Nios II 自定义组件开发.assets/1586495564767.png)

双击 nios2 模块打开配置界面，进入 Vectors 页，设置 Reset vector memory 和 Exception vector memory 都为 on_chip memory：

![1586495632697](Nios II 自定义组件开发.assets/1586495632697.png)

按前述方法保持当前设计后，选择菜单项 Generate > Generate HDL...，在弹出窗口保持默认配置并且点击 Generate 按钮：

![1586483965517](Nios II 自定义组件开发.assets/1586483965517.png)

# Quartus 工程顶层模块设计

## 添加 qsys 模块

回到 Quartus 软件，选择菜单项 Project > Add/Remove Files in Project，弹出窗口中点击 ... 按钮，选择 Platform Designer 保存的后缀名为 qsys 的设计文件，完成后点击 OK 按钮：

![1586486724325](Nios II 自定义组件开发.assets/1586486724325.png)

在 Project Navigator 的 IP Components 页可以看到添加的 nios_kernel 模块

![1586486806951](Nios II 自定义组件开发.assets/1586486806951.png)

## 添加时钟模块

在 IP Catalog 界面选择并双击 PLL Intel FPGA IP：

![1586487135047](Nios II 自定义组件开发.assets/1586487135047.png)

在弹出窗口中设置 IP 名称，并点击 OK 按钮：

![1586487182006](Nios II 自定义组件开发.assets/1586487182006.png)

之后弹出的 IP 配置界面中设置参考时钟为 50 MHz（板上时钟），输出时钟为 50 MHz（[作为 qsys 模块参考时钟](#用 Platform Designer 建立 qsys 模块)），其它配置项保持默认并点击 Finish 按钮：

![1586487361445](Nios II 自定义组件开发.assets/1586487361445.png)

之后弹出 Generation 窗口提示 IP 生成结束，点击 Exit：

![1586487679526](Nios II 自定义组件开发.assets/1586487679526.png)

在弹出的提示窗口中保持默认，点击 Yes，将刚生成的 IP 添加至工程：

![1586487743045](Nios II 自定义组件开发.assets/1586487743045.png)

![1586487772869](Nios II 自定义组件开发.assets/1586487772869.png)

## 建立并编写顶层 HDL 模块

选择菜单项 File > New，在弹出窗口中选择 Verilog HDL File，点击 OK

![1586486925703](Nios II 自定义组件开发.assets/1586486925703.png)

在生成的 v 文件中编写顶层 HDL 模块，模块名称与建立 Quartus 工程时设置的顶层模块名称一致。

**注意：PLL IP 的模块端口定义在同名的 v 文件中，qsys 模块的端口定义在 Platform Designer 的菜单项 Generate > Show Instantiation Template 中。**

代码如下：

```verilog
module cus_com
(
	input clk_50m
);
	//时钟生成
	wire clk;
	wire locked;

	pll_50mhz pll_u
	(
	.refclk(clk_50m),
	.rst(1'b0),
	.outclk_0(clk),
	.locked(locked)
	);
	
	//nios 例化
	nios_kernel nios_u
	(
	.clk_clk       (clk),       //    clk.clk
	.reset_reset_n (locked) //  reset.reset_n
	);

endmodule
```

# 编译工程

## 综合

在 Tasks 界面双击 Analysis & Synthesis

![1586488440244](Nios II 自定义组件开发.assets/1586488440244.png)

## 管脚分配

完成综合后，Tasks 界面提示如下：

![1586488669468](Nios II 自定义组件开发.assets/1586488669468.png)

选择菜单项 Assignments > Pin Planner，在弹出窗口中设置 clk_50m 的 Location 为 PIN_U10，并设置全部管脚的 I/O Standart 为 3.3 V LVTTL

![1586488873801](Nios II 自定义组件开发.assets/1586488873801.png)

完成配置后关闭 Pin Planner 窗口。

## 添加时钟约束

在 Tasks 界面双击 Timing Analysis

![1586488938012](Nios II 自定义组件开发.assets/1586488938012.png)

完成后，选择菜单项 Tools > Timing Analyzer

在弹出窗口中双击 Create Timing Netlist：

![1586489165471](Nios II 自定义组件开发.assets/1586489165471.png)

在该窗口选择菜单项 Constraints > Create Clock，设置时钟名称和周期后点击 ... 按钮选中时钟管脚 clk_50m，完成后点击 Run 按钮：

![1586489353420](Nios II 自定义组件开发.assets/1586489353420.png)

在当前窗口双击 Update Timing Netlist：

![1586489414760](Nios II 自定义组件开发.assets/1586489414760.png)

双击 Write SDC File...

![1586489443895](Nios II 自定义组件开发.assets/1586489443895.png)

在弹出窗口中保持默认，并点击 OK 按钮：

![1586489473419](Nios II 自定义组件开发.assets/1586489473419.png)

关闭 Timing Analyzer 窗口，回到 Quartus。

选择菜单项 Assignments > Settings，进入 Timing Analyzer 页，选择并添加生成的 SDC 文件，完成后点击 OK： 

![1586489637585](Nios II 自定义组件开发.assets/1586489637585.png)

在 Tasks 界面双击 Compile Design

![1586489690067](Nios II 自定义组件开发.assets/1586489690067.png)

等待完成编译

![1586490026011](Nios II 自定义组件开发.assets/1586490026011.png)

# Nios 软件

选择菜单项 Tools > Nios II Software Build Tools for Eclipse（后文简称 SBT，与官方文档一致）

在 Quartus 工程目录下建立 workspace：

![1586490146875](Nios II 自定义组件开发.assets/1586490146875.png)

在 SBT 选择菜单项 File > New > Nio II Application and BSP from Template，在弹出窗口中选择 Quartus 工程目录下的 nios_kernel.sopcinfo 文件，并设置工程名称，并选择 Hello World 模板，完成后点击 Finish 按钮：

![1586494947605](Nios II 自定义组件开发.assets/1586494947605.png)

通过 Project Explorer 双击打开 hello_world.c 文件：

![1586495217000](Nios II 自定义组件开发.assets/1586495217000.png)

修改代码如下：

```c
#include <stdio.h>
#include <unistd.h>

int main()
{
	while (1)
	{
		printf("Hello from Nios II!\n");
		usleep(500000);
	}
	return 0;
}
```

在 Project Explorer 中选择 nios_app_bsp 工程，在右键菜单选择 Build Project；完成后，同样在 nios_app 工程上选择 Build Project。

# 加载运行基础工程

连接 USB Blaster 加载器和开发板后，开发板上电。

在 Quartus 选择菜单项 Tools > Programmer，在 Programmer 窗口点击 Auto Connect，并在弹出窗口中选中 5CSEBA2，并点击 OK 按钮：

![1586496263621](Nios II 自定义组件开发.assets/1586496263621.png)

在 FPGA 器件点击右键，选择菜单项 Change File

![1586496346045](Nios II 自定义组件开发.assets/1586496346045.png)

在 Select New Programming File 窗口中选择 Quartus 工程目录下 output_files 文件夹中的 cus_com.sof 文件，完成后选中 Program/Configure，并点击 Start 按钮：

![1586496449822](Nios II 自定义组件开发.assets/1586496449822.png)

完成 FPGA 程序载入后，Progress 显示载入成功

![1586496492740](Nios II 自定义组件开发.assets/1586496492740.png)

关闭 Programmer。

回到 SBT 软件，在 nios_app 工程上右键菜单选择 Run As > Run Configurations：

![1586496706445](Nios II 自定义组件开发.assets/1586496706445.png)

在弹出窗口中右键点击 Nio II Hardware，选中 New 选项，之后进入 Target Connection 页，点击 Refresh Connections 按钮找到 USB Blaster，最后点击 Run 按钮：

![1586496858798](Nios II 自定义组件开发.assets/1586496858798.png)

在 SBT 界面的 Nios II Console 显示来源于 JTAG UART 的调试信息：

![1586496914436](Nios II 自定义组件开发.assets/1586496914436.png)

# 用 Platform Designer 建立自定义组件

打开 Platform Desgner，并且在 IP Catalog 双击 New Component

![1586506487091](Nios II 自定义组件开发.assets/1586506487091.png)

在弹出窗口中配置组件：

1. 在 Component Type 页设置组件名称和显示名称，以及 Group

![1586506600822](Nios II 自定义组件开发.assets/1586506600822.png)

2. 在 Signal & Interfaces 页，双击 add interface 添加接口

![1586506753208](Nios II 自定义组件开发.assets/1586506753208.png)

3. 在 Signal & Interfaces 页，进入每个 interface 的配置界面进行配置，主要是时钟与复位
4. 在 Signal & Interfaces 页，在每个 interface 下双击 add signal 添加接口信号，并配置
   - 由于 avalon_slave 只用于写入数据，因此只有地址（4 位）、片选、写使能和写数据（8 位）

5. 在 Files 页，点击 Create Synthesis File from Signals，根据提示建立关联 HDL 文件，并且依据 Signal & Interfaces 页的设置生成模块端口
   - 比较麻烦的是不能修改默认文件名，可以在生成文件后，用 Remove File 移除默认文件，修改文件名和内部模块名后再用 Add File 添加

![1586507245335](Nios II 自定义组件开发.assets/1586507245335.png)

![1586507337735](Nios II 自定义组件开发.assets/1586507337735.png)

按以下代码修改建立的 HDL 文件：

```verilog
// new_component.v

// This file was auto-generated as a prototype implementation of a module
// created in component editor.  It ties off all outputs to ground and
// ignores all inputs.  It needs to be edited to make it do something
// useful.
// 
// This file will not be automatically regenerated.  You should check it in
// to your version control system if you want to keep it.

`timescale 1 ps / 1 ps
module new_component (
		input  wire [3:0] avalon_slave_address,    // avalon_slave.address
		input  wire       avalon_slave_chipselect, //             .chipselect
		input  wire       avalon_slave_write,      //             .write
		input  wire [7:0] avalon_slave_writedata,  //             .writedata
		input  wire       reset_sink_reset,        //   reset_sink.reset
		input  wire       clock_sink_clk,          //   clock_sink.clk
		output wire       conduit_end_led_out      //  conduit_end.new_signal
	);

	// TODO: Auto-generated HDL template
	wire clk;
	assign clk = clock_sink_clk;
	wire rst;
	assign rst = reset_sink_reset;
	
	//输出 LED 值
	reg led = 1'b0;

	always @(posedge clk or posedge rst) begin
		if (rst == 1'b1) begin
			led <= 1'b0;
		end
		else begin
			case ({avalon_slave_chipselect, avalon_slave_write, avalon_slave_address})
				{1'b1, 1'b1, 4'b1111}: begin
					case (avalon_slave_writedata)
						8'hFF: begin
							led <= 1'b1;
						end

						8'h0: begin
							led <= 1'b0;
						end

						default: begin
							//保持
							led <= led;
						end
					endcase
				end

				default: begin
					//保持
					led <= led;
				end
			endcase
		end
	end

	assign conduit_end_led_out = led;

endmodule

```



完成上述步骤后，点击 Finish 按钮。

在 IP Catalog 中双击刚才建立的组件，点击 Finish 按钮将组件添加至 System Contents 中

![1586507434428](Nios II 自定义组件开发.assets/1586507434428.png)

![1586507497494](Nios II 自定义组件开发.assets/1586507497494.png)

在菜单中选择 System > Assign Base Addresses，之后选择 Generate > Generate HDL，生成 qsys 模块

# 修改 Quartus 工程

向顶层模块中的 qsys 模块添加输出 LED 端口，并连接至 FPGA 管脚，代码如下：

```verilog
module cus_com
(
	input clk_50m,
	output led0,
	output led1
);
	//时钟生成
	wire clk;
	wire locked;

	pll_50mhz pll_u
	(
	.refclk(clk_50m),
	.rst(1'b0),
	.outclk_0(clk),
	.locked(locked)
	);
	
	//nios 例化
	nios_kernel nios_u
	(
	.clk_clk       (clk),       //    clk.clk
	.reset_reset_n (locked), //  reset.reset_n
	.led_out_new_signal (led0)  // led_out.new_signal
	);
	
	assign led1 = ~led0;

endmodule
```

按照与前述相同的办法在 Pin Planner 中设置 led0 和 led1 管脚的位置

![1586507682638](Nios II 自定义组件开发.assets/1586507682638.png)

在 Quartus 中直接双击 Tasks 界面的 Compile Design 完成编译，不需要再次添加时钟约束。

# 修改 nios 软件

完成 Quartus 工程的编译后，打开 SBT 内的  workspace。

在 nios_app_bsp 工程选择，Generate BSP 后，再次运行 Build Project

![1586508455330](Nios II 自定义组件开发.assets/1586508455330.png)

按以下代码修改 hello_world.c 后，在 nios_app 工程选择 Build Project

```c
#include <stdio.h>
#include <unistd.h>

#include "../nios_app_bsp/system.h"
#include "alt_types.h"

int main()
{
	alt_u8* ptr_led = AVALON_LED_0_BASE+15;

	while (1)
	{
		printf("Hello from Nios II!\n");
		usleep(250000);
		*ptr_led = 255;
		usleep(250000);
		*ptr_led = 0;
	}
	return 0;
}
```

按[前述相同的办法加载并运行程序](#加载运行基础工程)，发现板上 2 个 LED 按设计闪烁。