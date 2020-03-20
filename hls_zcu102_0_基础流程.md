本系列文档通过实例说明 Vivado HLS 的基础开发流程。

遗憾的是，HLS 没有必要进行更为深入的研究。因为在本人看来，HLS 仅仅是一个在 FPGA 上快速实现算法的工具，稍有些复杂的算法就几乎无法得到足够的运行效率。原本对 HLS 的幻想是像 C/C++ 代码一样在 FPGA 中执行计算，但是在后续的进阶实验中越发感受到 HLS 的局限，无论是代码本身的优化或者使用 Directive 配置都没有理想的结果。可能 HLS 面向的使用对象是不了解 FPGA 运行原理的 C/C++ 工程师，仅在对代码运行效率没有要求的情况下使用。

开发环境：

-   zcu102 开发板
-   Windows 10
-   Vivado 2018.2.1
-   Visual Studio Code
-   Matlab 2017b

主要参考资料包括：

- ug998
- ug871
- **ug902**
- ug1197
- [Vivado HLS 基本应用与图像处理视频教程](https://www.bilibili.com/video/av46063188?p=1)

---

本文档说明了使用 Vivado HLS 实现算法功能的基本流程，并且配置 VS Code 为基础的代码编辑器（仅用于编辑代码）。

# 建立 HLS 工程

Vivado Design Suite 完整安装后，在 Win10 开始菜单中打开 HLS：

![image-20200205163051777](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205163051777.png)

HLS 启动后，在菜单中选择 File > New Project

![image-20200205163259835](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205163259835.png)

在弹出窗口中选择工程名称和路径，点击 Next 按钮

![image-20200205163925614](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205163925614.png)

后续的添加源文件（source files）和测试文件（testbench files）界面默认不做操作，直接按 Next 按钮

![image-20200205164116705](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205164116705.png)

![image-20200205164129235](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205164129235.png)

在 solution 界面设置解决方案名称、时钟频率，并选择器件，最后点击 Finish 按钮

注意：同一个 HLS 工程对应一组源代码和测试代码，但是可以包含多个解决方案，每个解决方案可以对代码的 FPGA 实现做不同的配置。

![image-20200205165143777](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205165143777.png)

工程建立后的 Explorer 界面如下：

![image-20200205165406036](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205165406036.png)

# 配置 VS Code

## 配置 include 路径

首先确保 VS Code 已安装 C/C++ 插件和 C++ Intellisense 插件

![image-20200205170222863](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205170222863.png)

进入 HLS 工程所在目录，在右键菜单选择“通过 Code 打开”

![image-20200205165826741](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205165826741.png)

在 VS Code 中通过菜单或者快捷键打开 Command Palatte

![image-20200205170016251](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205170016251.png)

在弹出的 Command Palatte 中输入 edit，并选择：

==C/C++: Edit Configurations (UI)==

![image-20200205170339057](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205170339057.png)

在配置界面的包含路径中添加 HLS 工程 Includes 下全部的路径

注意：${workspaceFolder}/** 路径是点击输入框自动添加

![image-20200205170818513](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205170818513.png)

其它配置项保持默认，关闭配置界面。

在 HLS 工程文件夹内添加了 .vscode 文件夹，打开其中的c_cpp_properties.json 即为前述的配置内容

![image-20200205171104186](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205171104186.png)

## 配置默认文件头

参考：<https://www.jianshu.com/p/07a7fd95954f>

在 VS Code 菜单选择 File > Preferences > User Snippets

![image-20200205171459121](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205171459121.png)

在弹出命令框中选择 C++

![image-20200205171609361](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205171609361.png)

在打开的 cpp.json 文件中添加以下内容，通过关键词 hls 添加与 Vivado 建立 HDL 文件相同的文件头

```json
{
	"HLS CPP Title": {
		"prefix": "hls",
		"body": [
			"//////////////////////////////////////////////////////////////////////////////////",
			"// Company:",
			"// Engineer:",
			"//",
			"// Create Date: $CURRENT_YEAR/$CURRENT_MONTH/$CURRENT_DATE $CURRENT_HOUR:$CURRENT_MINUTE:$CURRENT_SECOND",
			"// Design Name:",
			"// Module Name: $TM_FILENAME_BASE",
			"// Project Name:",
			"// Target Devices:",
			"// Tool Versions:",
			"// Description:",
			"//",
			"// Dependencies:",
			"//",
			"// Revision:",
			"// Revision 0.01 - File Created",
			"// Additional Comments:",
			"//",
			"//////////////////////////////////////////////////////////////////////////////////",
			"\n",
			"\n"
		],
		"description": "HLS CPP Title"
	}
}
```

注意：prefix 后的内容是 snippet 的提示文字，在 cpp 文件中输入 hls 后在弹出的 suggestion 中选择 HLS CPP Title。当然必须保证在 VS Code 中打开 suggestion 功能。

![image-20200205175305445](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205175305445.png)

# 编写代码

**一般情况下 HLS 工程里的源文件都使用 C++，而不是 C，原因是 C++ 可以通过模板功能自定义的整数位宽和函数功能。**

源文件的功能是定义一个函数，将输入的 8 位 led 数值循环移位，Verilog 代码如下：

```verilog
led_out[7:0] <= {led_in[6:0], led_in[7]};
```

在 VS Code 的 Explorer 的右键菜单中选择 New FIle

![image-20200205175613635](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205175613635.png)

建立源代码文件： shift_led.cpp 、shift_led.h

测试代码文件：tb_shift_led.cpp

## 代码：shift_led.h

```c++
#ifndef _SHIFT_LED_H_
#define _SHIFT_LED_H_

// 时钟频率为 125 MHz，for 循环 PERIOD 次即为 1 秒
#define PERIOD 125000000

// 函数声明
void shift_led(unsigned char* led_out, unsigned char led_in);

#endif

```



## 代码：shift_led.cpp

```c++
#include "shift_led.h"

void shift_led(unsigned char* led_out, unsigned char led_in)
{
	// 保存输入数值
	unsigned char temp;
	temp = led_in;

	// 用 for 循环计时 1 秒
	unsigned int i;

	for (i = 0; i < PERIOD; ++i)
	{
		;
	}

	// 循环移位 temp <= {temp[6:0], temp[7]};
	temp = (temp << 1) | (temp >> 7);

	//输出数据
	*led_out = temp;
}

```



## 代码：tb_shift_led.cpp

```c++
#include <stdio.h>
#include "shift_led.h"

// 运行 HLS 函数的次数 
#define TIME 17

// 人工观察运行结果
int main()
{
	unsigned char led_in = 0x7F;
	unsigned char led_out = 0;

	for (int i = 0; i < TIME; ++i)
	{
		// 调用 HLS 函数
		shift_led(&led_out, led_in);

		// 将 HLS 函数输出值赋给输入值用于下一轮计算
		led_in = led_out;

		// 打印输出值
		printf("current led value: %x\n", led_in);
	}

	// 返回 0 值表示测试正确，非 0 值表示测试错误
	return 0;
}

```



## 向 HLS 工程添加代码

HLS 工程的代码包括 2 种：源代码（Source）和测试代码（Test Bench）。

源代码用于综合生成 RTL 代码在 FPGA 中运行，测试代码中实现 main 函数调用源代码中定义的函数。

回到 HLS 工程，在 Explorer 的 Source 项的右键菜单上选择 Add Files，并且在弹出窗口中选择前述的 shift_led.cpp

**注意：shift_led.h 文件不能添加进 HLS 工程**

![image-20200205211226142](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205211226142.png)

在 Explorer 的 Test Bench 项的右键菜单选择 Add Files，添加 tb_shift_led.cpp

![image-20200205211441818](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205211441818.png)

完成后的 Explorer 显示如下：

![image-20200205211525267](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205211525267.png)

在 HLS 工程中打开代码文件之前应当先配置 HLS 环境（Eclipse）使用 UTF-8 的文本编码格式与 VS Code 一致

VS Code 中的配置如下图所示：

![image-20200205212431244](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205212431244.png)

在 HLS 工程菜单中打开 Window > Preferences > Workspace 页，配置以下 2 项：

- Text file encoding: Other UTF-8
- New text file line delimiter: Other Unix

![image-20200205211918062](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205211918062.png)

在 HLS 工程中可以双击打开 cpp 代码文件

# C Simulation

在 HLS 菜单选择 Project > Run C Simulation

弹出窗口保持默认，点击 OK 按钮

![image-20200205213207879](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205213207879.png)

根据 Console 窗口提示，前述代码的仿真测试正确完成。

如果将 tb_shift_led.cpp 中 main 函数返回值改为 1，再次运行 C Simulation，则 Console 中提示仿真出错：

![image-20200205214638033](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205214638033.png)

注意：C Simulation 完成后在 Explorer 的 solution1 中出现了 csim 文件夹，在 report 中的 _csim.log 即为仿真测试的记录

![image-20200205214845823](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205214845823.png)

# C Synthesis

在 HLS 的菜单中选择 Project >  Project Settings

![image-20200205215728700](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205215728700.png)

在 Synthesis 页设置 Top Function 为 shift_led，点击 OK 按钮

![image-20200205215848731](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205215848731.png)

在 HLS 菜单选择 Solution > Run C Synthesis > Active Solution

![image-20200205220050331](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205220050331.png)

运行完成后，Explorer 的 solution1 中出现 impl 和 syn 文件夹，双击打开综合报告：shift_led_csynth.rpt

![image-20200205220628737](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205220628737.png)

在综合报告的最下方 Interface 为综合生成 RTL 模块的端口列表：

![image-20200205220946993](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205220946993.png)

也可以在 Explorer 中打开 solution1 > syn > verilog > shift_led.v 查看

![image-20200205220901696](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205220901696.png)

注意：此时生成的 RTL 文件根据 ug902 的说明，不建议直接用于 Vivado 工程使用，而是应当[生成 IP 供 Vivado 工程调用](#Vivado 工程)

# Run C/RTL Cosimulation

只有在完成 C Synthesis 之后才能进行 C 与 RTL 的联合仿真。

在 HLS 菜单选择 Solution > Run C/RTL Cosimulation

![image-20200205221400376](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205221400376.png)

在弹出窗口中选择 Vivado Simulator 和 Dump Trace all （用于记录仿真波形），点击 OK 按钮

![image-20200205221524367](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205221524367.png)

仿真完成后在 Explorer 的 solution1 中出现 sim 文件夹，打开仿真报告文件：

solution1 > sim > report > shift_led_cosim.rpt

显示通过仿真测试

![image-20200205222000521](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205222000521.png)

打开波形文件==（调用 Vivado）==：

solution1 > sim > verilog > shift_led.wdb

**注意：无法在 HLS 中双击打开，而必须在 Win10 文件夹中双击打开该文件**

![image-20200205222317651](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205222317651.png)

在 Vivado 中 Scope 页右键点击 inst_shift_led，选择 Add to Wave Window

![image-20200205222827024](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205222827024.png)

除此以外，时钟和复位信号如下图所示：

![image-20200205223228146](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205223228146.png)

添加完成后的波形如下图所示，用于观察生成的 RTL 模块的端口时序

![image-20200205223355921](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205223355921.png)

# ==修改==

在前文的波形中可以发现 1 个时钟周期就完成移位，而没有按照代码的设计等待 1 秒，很可能是[编译器将 shift_led.cpp 代码中空执行的 for 循环优化清理掉了](#代码：shift_led.cpp)。

于是修改 shift_led.cpp 代码如下：

```c++
#include "shift_led.h"

void shift_led(unsigned char* led_out, unsigned char led_in)
{
	// 保存输入数值
	unsigned char temp;
	temp = led_in;

	// 用 for 循环计时 1 秒
	unsigned int i;

	for (i = 0; i < PERIOD; ++i)
	{
		if (i == 0)
		{
			// 循环移位 temp <= {temp[6:0], temp[7]};
			temp = (temp << 1) | (temp >> 7);
		}
	}

	//输出数据
	*led_out = temp;
}

```

并且为了仿真方便，在 shift_led.h 文件中修改 PERIOD 数值如下：

```c++
#ifndef _SHIFT_LED_H_
#define _SHIFT_LED_H_

// 时钟频率为 125 MHz，for 循环 125000000 次即为 1 秒
// 为减少仿真时间，将 PERIOD 值改为 125
#define PERIOD 125

// 函数声明
void shift_led(unsigned char* led_out, unsigned char led_in);

#endif

```

代码修改完成后，重新执行以下步骤：

- [C Simulation](#C Simulation)
- [C Synthesis](#C Synthesis)
- [Run C/RTL Cosimulation](#Run C/RTL Cosimulation)

最后得到正确的波形：

![image-20200205225021902](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200205225021902.png)

# 导出 HLS IP

*在导出 HLS IP 之前，修改 shift_led.h 中的 PERIOD 数值为 125000000，以实现 1 秒延时。*

修改完成后，直接进行 [C Synthesis](#C Synthesis)。

C Synthesis 正确完成后，在 HLS 菜单中选择 Solution > Export RTL

![image-20200206075007508](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200206075007508.png)

在弹出窗口中如下图配置后，点击 OK 按钮

![image-20200206075143099](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200206075143099.png)

完成操作后，在 HLS 的 Explorer 的 solution1 项的 impl 目录下出现 ip 文件夹

![image-20200206075528257](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200206075528257.png)

# Vivado 工程

Vivado 的使用细节本文档不再详述，具体方法可以参考之前的文档：

[zuc102_0_PL 端 LED 闪烁](https://blog.csdn.net/botao_li/article/details/85257566)

## Vivado 工程导入 IP

建立 Vivado 工程 hls_test 之后，在 Flow Navigator 的 PROJECT MANAGER 中点击 Settings

![image-20200206075651155](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200206075651155.png)

在 IP > Repository 页点击 + 按钮添加 HLS 生成的 IP 文件夹，完成后点击 OK 按钮

![image-20200206075854007](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200206075854007.png)

在 Vivado 的 Flow Navigator 中点击 IP Catalog 打开 IP 列表界面

![image-20200206080045629](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200206080045629.png)

在搜索框内输入 shift_led 找到 HLS 生成的 IP

![image-20200206080136199](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200206080136199.png)

双击 IP，并且在弹出窗口中保持默认，点击 OK 按钮

![image-20200206080307068](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200206080307068.png)

根据提示，保持默认配置完成 IP 生成

在 Sources 框内出现刚生成的 IP

![image-20200206080419871](hls_zcu102_0_%E5%9F%BA%E7%A1%80%E6%B5%81%E7%A8%8B.assets/image-20200206080419871.png)

## HDL 代码实现

在 Vivado 工程中建立顶层模块 hls_top.v

添加以下代码，shift_led 的端口时序参考[仿真波形](#Run C/RTL Cosimulation)

```verilog
module hls_top(
	input clk_p,
	input clk_n,
	output [7:0] leds
	);

	wire clk;// 125MHz工作时钟
    
    // 用 IBUFDS 原语实现差分时钟转单端
    IBUFDS
    #(
    .IOSTANDARD("DEFAULT")// DEFAULT默认所有情况
    )
    IBUFDS_inst
    (
    .O(clk),// 输出
    .I(clk_p),// P端输入
    .IB(clk_n)// N端输入
    );

	// 用计数器产生复位信号
	reg [15:0] cnt_rst = 16'd0;
	
	wire rst;
	assign rst = ~(cnt_rst[15]);// 计数器最高位取反作为高有效复位信号

	always @(posedge clk) begin
		case (cnt_rst)
			16'hFFFF: begin
				// 计数结束，数值保持
				cnt_rst <= cnt_rst;
			end

			default: begin
				// 计数器增 1
				cnt_rst <= cnt_rst+16'd1;
			end
		endcase
	end

	// 例化 IP
	reg start = 1'b0;
	reg [7:0] led_in = 8'h7F;
	wire [7:0] led_out;
	wire led_out_vld;

	shift_led_0 shift_led_u (
	.ap_clk(clk),                  // input wire ap_clk
	.ap_rst(rst),                  // input wire ap_rst
	.ap_start(start),              // input wire ap_start
	.ap_idle(),                // output wire ap_idle
	.ap_done(),                // output wire ap_done
	.ap_ready(),              // output wire ap_ready
	.led_out(led_out),                // output wire [7 : 0] led_out
	.led_out_ap_vld(led_out_vld),  // output wire led_out_ap_vld
	.led_in(led_in)                  // input wire [7 : 0] led_in
	);

	// IP 功能控制
	always @(posedge clk) begin
		if (rst == 1'b1) begin
			start <= 1'b0;
			led_in <= 8'h7F;
		end
		else begin
			// 启动 IP 工作
			start <= 1'b1;

			// 产生有效输出 led_out 时，向 led_in 赋值
			if (led_out_vld == 1'b1) begin
				led_in <= led_out;
			end
			else begin
				led_in <= led_in;
			end
		end
	end

endmodule

```

之后按照与[zuc102_0_PL 端 LED 闪烁](https://blog.csdn.net/botao_li/article/details/85257566)文档相同的方法，添加引脚约束和时钟约束，并且生成 bit 配置文件。

# 测试

按照[zuc102_0_PL 端 LED 闪烁](https://blog.csdn.net/botao_li/article/details/85257566)文档的说明，在开发板的 FPGA 上烧写 bit 配置文件，观察板上 8 个 led 灯按照预期工作。

==TODO==：待上板测试。