**在SDK中开发PS程序时，如果改动工程配置中的Path and Symbols中Libraires参数，则可能出现链接时undefined reference的错误**

因为添加的静态库失效，没有链接，原因在于改动Libraries之后，gcc的链接指令多出一个-l指令，与SDK建立工程时默认的Libraries的指令格式不匹配，导致链接失败

比如新建工程时，在工程上右键菜单选择Properties，弹出的属性窗口内打开Paths and Symbols，进入Libraries页

![1552358450741](assets/1552358450741.png)

可以看到默认使用-Wl添加了xil, gcc和c这3个静态库

如果点击Cancel按钮关闭窗口，Build工程时链接指令为

>   aarch64-none-elf-gcc -Wl,-T -Wl,../src/lscript.ld -L../../dadfad_bsp/psu_cortexa53_0/lib -o "dadfad.elf"  ./src/helloworld.o ./src/platform.o   ==-Wl,--start-group,-lxil,-lgcc,-lc,--end-group==

链接正确，生成elf文件

如果点击OK按钮关闭窗口（没有任何改动），则Build工程师链接指令变为

>aarch64-none-elf-gcc -L../../dadfad_bsp/psu_cortexa53_0/lib -Wl,-T -Wl,../src/lscript.ld -o "dadfad.elf"  ./src/helloworld.o ./src/platform.o   ==-l-Wl,--start-group,-lxil,-lgcc,-lc,--end-group==

错误提示为

>d:/xilinx/sdk/2018.2/gnu/aarch64/nt/aarch64-none/bin/../lib/gcc/aarch64-none-elf/7.2.1/../../../../aarch64-none-elf/bin/ld.exe: cannot find -l-Wl,--start-group,-lxil,-lgcc,-lc,--end-group
>collect2.exe: error: ld returned 1 exit status
>make: *** [dadfad.elf] 错误 1

与正确的链接指令的差异在于在-Wl之前多出了-l，导致出现不可识别的指令，因此链接失败

**解决办法：**

删除默认的Libraries配置语句，改成常规的顺序添加方式，**注意静态库之间的依赖顺序**

![1552358875923](assets/1552358875923.png)

链接指令变为：

>   aarch64-none-elf-gcc -L../../dadfad_bsp/psu_cortexa53_0/lib -Wl,-T -Wl,../src/lscript.ld -o "dadfad.elf"  ./src/helloworld.o ./src/platform.o   ==-lc -lgcc -lxil==

链接正确完成