主要参考：ug1400

# 安装串口调试软件

安装串口软件 [tinyserial](https://github.com/lifimlt/tinyserial/releases)

![image-20200108144701256](vitis_by_zcu102_1_Hello%20Vitis.assets/image-20200108144701256.png)

本人**使用 Qt 5.12.6 编译**后使用（deb 安装后无法运行）。

# 建立 Platform Project

启动 Vitis Software Platform，设置 Workspace 路径后点击 Launch 按钮。

![image-20200109072120445](vitis_by_zcu102_1_Hello%20Vitis.assets/image-20200109072120445.png)

进入 IDE 后在菜单栏选择 File > New > Platform Project

设置 Project name 后点击 Next 按钮

![image-20200109072608859](vitis_by_zcu102_1_Hello%20Vitis.assets/image-20200109072608859.png)

选中 Create from hardware specification (XSA) 后，点击 Next 按钮

![image-20200109072714562](vitis_by_zcu102_1_Hello%20Vitis.assets/image-20200109072714562.png)

点击 Browse 按钮后在以下路径选择 zcu102 的默认 XSA 文件

选择 Operating system 为 standalone

选择 Processor 为 psu_cortexa53_0

点击 Finish 按钮

![image-20200109073010314](vitis_by_zcu102_1_Hello%20Vitis.assets/image-20200109073010314.png)

进入 Eclipse 的 Desgin 界面

![image-20200109073349266](vitis_by_zcu102_1_Hello%20Vitis.assets/image-20200109073349266.png)

在 zcu102_platform 工程打开右键菜单选择 Build Project

完成后显示

![image-20200109073710872](vitis_by_zcu102_1_Hello%20Vitis.assets/image-20200109073710872.png)

# 建立 Application Project

在菜单栏选择 File > New > Application Project

弹出窗口中设置工程名称，然后点击 Next 按钮

![image-20200109075511983](vitis_by_zcu102_1_Hello%20Vitis.assets/image-20200109075511983.png)

选中之前建立的 Platform Project，然后点击 Next 按钮

![image-20200109075649729](vitis_by_zcu102_1_Hello%20Vitis.assets/image-20200109075649729.png)

接下来的页面保持默认，点击 Next 按钮

![image-20200109075729098](vitis_by_zcu102_1_Hello%20Vitis.assets/image-20200109075729098.png)

选中 Hello World 模板后点击 Finish 按钮

![image-20200109075808052](vitis_by_zcu102_1_Hello%20Vitis.assets/image-20200109075808052.png)

在 Explorer 中出现 hello 工程，双击打开 helloworld.c

![image-20200109075923553](vitis_by_zcu102_1_Hello%20Vitis.assets/image-20200109075923553.png)

修改原始代码为框内代码

![image-20200109080011539](vitis_by_zcu102_1_Hello%20Vitis.assets/image-20200109080011539.png)

在 hello 工程的右键菜单中选择 Build Project

# 验证

连接 zcu102 板卡的 JTAG 线和 UART 转 USB 线至主机后给板卡上电

打开 tinyserial 软件，按下图配置并且点击 open 按钮

![image-20200109080409969](vitis_by_zcu102_1_Hello%20Vitis.assets/image-20200109080409969.png)

在 hello 工程的右键菜单中选择 Run As > Launch on Hardware (Single Application Debug)

在 tinyserial 中收到 Hello Vitis 的串口数据

![image-20200109080629118](vitis_by_zcu102_1_Hello%20Vitis.assets/image-20200109080629118.png)