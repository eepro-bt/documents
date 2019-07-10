# 硬件环境

主板：Asus Z370-P II

CPU：Intel I7 8700k

硬盘：256GB NVMe SSD + 2TB 机械硬盘

# Windows 10系统下创建安装u盘

在Ubuntu官网下载iso镜像文件，并根据安装指导下载Rufus 3.5.1497用于创建安装u盘

插入u盘（保证空间足够），打开Rufus界面后，选择下载的iso镜像文件

分区类型：GPT

目标系统类型：UEFI（非CSM）

**文件系统：FAT32（默认）**

簇大小：16K字节（默认）

注意：文件系统不能选择NTFS，否则会在u盘启动后进入grub命名行（无法找到引导）

根据Rufus提示，选择iso模式完成安装u盘创建

# 启动Ubuntu安装界面

将安装u盘插入目标主机

主机上电启动后进入bios的高级设置界面

进入启动页，设置快速启动为Disabled

进入启动页内的安全启动菜单，选择"清除安全启动密钥"，用这种方式关闭安全启动（bios中不能直接关闭secure boot）

**进入高级页的CPU设置，在Power Management Control中设置涡轮加速模式为关闭**

如果不关闭CPU的涡轮加速模式，u盘启动选择安装ubuntu后会出现mce Hardware Error

最后在bios中设置u盘启动，保存设置并退出bios

# Ubuntu硬盘分区

u盘启动后，在界面选择Install Ubuntu

根据提示执行安装，注意选择**自定义分区**：

-   efi分区：主分区：SSD硬盘：500MB
-   swap分区：主分区：SSD硬盘：至少为当前内存总量的50%（按需配置大小，或者不分配swap）
-   ext4分区：挂载点/：主分区：SSD硬盘：SSD内全部剩余空间
-   ext4分区：挂载点/home：主分区：机械硬盘：机械硬盘全部空间