# 前言

Vitis 被 Xilinx 称为 “统一软件平台”，从目前已了解的情况来看，嵌入式软件开发在 Vitis 上更为简单方便。

从 ISE 到 Vivado，再到 Vitis 的升级趋势来看，Vitis 将是未来数年的开发主力。

虽然 2019.2 的首发版本可能会有许多 bug，但是试用 Vitis 已足够。

**Vitis by ZCU102** 系列文章将在 zcu102 开发板上尽可能试用 Vitis Embedded 的各种功能。

主要参考的官方文档包括：

-   ug1400
-   ug1209
-   ug1165
-   ug1393

# 开发环境

硬件环境：zcu102 开发板

软件环境：ubuntu 18.04.3、Vitis 2019.2.1

虽然 ug1400 中只提及支持 ubuntu 18.04.2，但是以下所有内容都基于 18.04.3 版本。

注意：根据 ug1400 的说明 Windows 系统对 Vitis 的嵌入式开发不能完全支持

>   Note: Windows OS support is limited to the Vitis embedded software development flow.

# 安装步骤

## 安装 opencl

>   sudo apt-get update
>
>   sudo apt-get install ocl-icd-libopencl1 opencl-headers ocl-icd-opencl-dev

## 下载并安装 Vitis Software Platform

以下网页日期为 20200108

进入 [Xilinx 中国网站](https://china.xilinx.com/)

打开 [Vitis 统一软件平台](https://china.xilinx.com/products/design-tools/vitis/vitis-platform.html)

![image-20200108141029901](vitis_by_zcu102_0_%E5%BB%BA%E7%AB%8B%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83.assets/image-20200108141029901.png)

进入 [立即下载]()

![image-20200108141106799](vitis_by_zcu102_0_%E5%BB%BA%E7%AB%8B%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83.assets/image-20200108141106799.png)

选择 **Vitis(软件开发者)**页面最下端的离线安装包

![image-20200108141328543](vitis_by_zcu102_0_%E5%BB%BA%E7%AB%8B%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83.assets/image-20200108141328543.png)

以及页面最上端的更新包

![image-20200108141429408](vitis_by_zcu102_0_%E5%BB%BA%E7%AB%8B%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83.assets/image-20200108141429408.png)

使用 Xilinx 账号下载解压，并且根据提示按照正常流程安装。**注意：安装过程中应当勾选所有可安装组件。**

## 安装加载线驱动

参考 ug973 的 Install Cable Driver 部分的说明。

>   cd 
>   <Vivado Install Dir>/data/xicom/cable_drivers/lin64/
>
>   install_script/install_drivers/
>
>   sudo ./install_drivers

完成安装后 Terminal 显示

>   INFO: Driver installation successful.

## 安装 Xilinx Runtime (XRT)

根据 ug1400 的说明[下载 XRT](https://www.xilinx.com/bin/public/openDownload?filename=xrt_201920.2.3.1301_18.04-xrt.deb) 并安装

>   sudo apt install <deb-dir>/<xrt_filename_OS>.deb

## 设置运行路径

>source <VITIS_INSTALL_DIR>/Vitis/2019.2/settings64.sh
>
>source <XRT_INSTALL_DIR>/xilinx/xrt/setup.sh