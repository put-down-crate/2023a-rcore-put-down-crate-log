# 2023a-rcore-put-down-crate-log
2023年秋冬季开源操作系统训练营第二阶段的日常学习记录

## CH1 祖传Hello, World！  
## 总结：
一：github的访问不够通畅，影响偏大。我这github.io没有打开过。
二：感觉第零章的导引功能需要增强，比如项目布局、代码结构、相关资料、相关配置、以及如何应对意外情况，后续进度章节需要注的前提要件，都可以放到第零章，使刷过第零章后就知道本训营进度的整体需要把握、细节需要注意的事项。  
三：本章要求实验均默认已有代码完成，但是需要熟悉项目代码结构。
三：本章节需手动完成的是bootloader替换，完成关机代码的替换。  
四：预备知识没有提前顾备，刚开始比较蒙。

### 1. 点击邀请链接创建仓库
#### 生成仓库并克隆到本地
``git clone https://github.com/LearningOS/2023a-rcore-put-down-crate.git``
### 2. 运行rCore，发现问题
使用命令  
``$ cd 2023a-rcore-put-down-crate``  
``$ git checkout ch1``  
``$ cd os``  
``$ make run``  
#### 编译运行停在给出平台信息qemu，似乎没有进入引导。  
所有硬件加电自检后都要将硬件控制权交由引导程序，再由引导程序引导软件系统进行启动，通过驱动程序与相关硬件进行交互，达成软件系统的运行状态。  
qemu做为硬件模拟器，那么问题出现的位置大概在qemu与bootloader之间。  
#### ch0已经验证实验环境没有问题，所以大概率bootloader是有点问题的。
#### 建立 gdb 软链接 与 make 文件调用匹配
``sudo ln -s /usr/sbin/riscv64-elf-gdb /usr/sbin/riscv64-unknown-elf-gdb``

### 3. 替换bootloader
从仓库
``https://github.com/rustsbi/rustsbi``
下载最后发布的rustsbi release版本 & debug版本。  
解压release版本得到文件 ``rustsbi-qemu.bin``，将此文件复制到
``2023a-rcore-put-down-crate/bootloader``目录下覆盖原有文件，怕出错可以先把原文件改名做备份。  
然后进入``2023a-rcore-put-down-crate/os``目录，运行命令:  
``$ make run``  
编译运行结果：
````
[rustsbi] RustSBI version 0.2.2, adapting to RISC-V SBI v1.0.0
.______       __    __      _______.___________.  _______..______   __
|   _  \     |  |  |  |    /       |           | /       ||   _  \ |  |
|  |_)  |    |  |  |  |   |   (----`---|  |----`|   (----`|  |_)  ||  |
|      /     |  |  |  |    \   \       |  |      \   \    |   _  < |  |
|  |\  \----.|  `--'  |.----)   |      |  |  .----)   |   |  |_)  ||  |
| _| `._____| \______/ |_______/       |__|  |_______/    |______/ |__|

[rustsbi] Implementation: RustSBI-QEMU Version 0.1.1
[rustsbi-dtb] Hart count: cluster0 with 1 cores
[rustsbi] misa: RV64ACDFHIMSU
[rustsbi] mideleg: ssoft, stimer, sext (0x1666)
[rustsbi] medeleg: ima, ia, bkpt, la, sa, uecall, ipage, lpage, spage (0xb1ab)
[rustsbi] pmp0: 0x10000000 ..= 0x10001fff (rw-)
[rustsbi] pmp6: 0x2000000 ..= 0x200ffff (rw-)
[rustsbi] pmp12: 0xc000000 ..= 0xc3fffff (rw-)
[rustsbi] enter supervisor 0x80200000
[kernel] Hello, world!
````
kernel给出了祖传helloworld，大概算正常运行了吧。



### 4.按照要示编写代码
#### 4.1. 完成实验指导书中的内容并在裸机上实现 hello world 输出
本章代码默认已实现
#### 4.2. 实现彩色输出宏 (只要求可以彩色输出，不要求 log 等级控制，不要求多种颜色)
本章代码默认已实现
#### 4.3. 隐形要求
可以关闭内核所有输出。从 lab2 开始要求关闭内核所有输出（如果实现了 log 等级控制，那么这一点自然就实现了）。
#### 4.4.利用彩色输出宏输出 os 内存空间布局
输出 .text、.data、.rodata、.bss 各段位置，输出等级为 INFO。
本章代码默认已实现
#### 4.5. challenge: 支持多核，实现多个核的 boot
#### 4.6 已修改代码完成输出后关机

### 5. 本章知识关注点
#### 5.1. RISC-V的两种特权模式
S-Mode Supervisor模式=》操作系统使用特权级别可执行特权指令  
M-Mode Machine模式=》特权级别比S-Mode更高，可以访问RISC-V处理器中所有系统资源
#### 5.2. 引导后的世界
##### 5.2.1. RustSBI(bootloader)完成基本硬件初始化====》》》》  
##### 5.2.2. 建立栈空间====》》》》  
##### 5.2.3. 清零bss段====》》》》  
##### 5.2.4. 跳转到app====》》》》  
##### 5.2.5. 函数调用<-->OS服务响应

#### 5.3. 现代编译器工具集（以 C 或 Rust 编译器为例）的主要工作流程

##### 5.3.1. 源代码（source code）–> 
##### 5.3.2. 预处理器（preprocessor）–> 
##### 5.3.3. 宏展开的源代码–> 
##### 5.3.4. 编译器（compiler）–> 
##### 5.3.5. 汇编程序–> 
##### 5.3.6. 汇编器（assembler）–> 
##### 5.3.7. 目标代码（object code）–> 
##### 5.3.8. 链接器（linker）–> 
##### 5.3.9. 可执行文件（executables）

#### 5.5. Rust相关
##### 5.5.1. 没有入口函数main，需在main.rs文件头添加``#![no_main]``标识
##### 5.5.2. 没有标准库依赖，需要在main.rs文件头添加``#![no_std]``标识
##### 5.5.3. 真正的入口函数需标注``#[no_mangle]``防止编译器混淆函数名
##### 5.5.4. 对于产生不可恢复错误的函数需要标注 ``#[panic_handler]``
##### 5.5.5. 需要适当熟悉 Rust 核心库
https://www.rustwiki.org.cn/zh-CN/core/index.html

#### 5.6 端序
将一个多位数的低位放在较小的地址处，高位放在较大的地址处，则称小端序（little-endian）；反之则称大端序（big-endian）。  
##### 常见的 x86、RISC-V 等架构采用的是小端序

#### 5.7. 内存地址对齐
对于 RISC-V 处理器而言，load/store 指令进行数据访存时，数据在内存中的地址应该对齐。如果访存 32 位数据，内存地址应当按 32 位（4 字节）对齐。  
如果数据的地址没有对齐，执行访存操作将产生异常。这也是在学习内核编程中经常碰到的一种bug。


#### 5.8. QEMU启动相关
三个地址:  
QEMU开始执行        -->   物理地址0x1000
bootloader开始执行  -->  物理地址0x80000000
内核开始执行        -->   物理地址0x80200000
##### 5.8.1. Qemu模拟的virt硬件平台上，物理内存的起始地址为0x80000000，物理内存默认大小为128MB 可以通过-m配置。
##### 5.8.2. 做为bootloader的rustsbi_qemu.bin加载到物理内存以物理地址0x80000000开头的区域上。
##### 5.8.3. 内核镜像os.bin加载到以物理地址0x80200000开头的区域上。
##### 5.8.4. QEMU模拟启动流程的三个阶段
###### 5.8.4.1. 由固化在QEMU内的一小段汇编程序负责
QEMU CPU 程序计数器初始化为0x1000, 因此QEMU第一条指令在物理地址0x1000, 第一阶段指令完成后，跳转到物理地址0x80000000，此地址是固化在QEMU的，不通过源码无法修改。
###### 5.8.4.2. 由bootloader负责
本阶段自物理地址0x80000000开始执行，主要执行rustsbi_qemu.bin加载到本段的内容，然后跳转到物理地址0x80200000。
###### 5.8.4.3. 由内核镜像负责
本段自物理地址0x80200000开始执行，主要执行内核镜像加载到本段的内容。

#### 5.9. 程序内存布局
High Address&nbsp;| stack&nbsp;&nbsp;&nbsp;|^^^^^^^^^^^^^^^^  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| heap&nbsp;&nbsp;&nbsp;&nbsp;|  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| .bss&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   Data Memory  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| .data&nbsp;&nbsp;&nbsp;&nbsp;|  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| .rodata |_________________________  
Low Address   | .text&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   Code Memory   


##### 5.9.1. 代码部分段放在最低地址的.text段
##### 5.9.2. 只读-已初始化全局数据，放入再高一点的.rodata段
##### 5.9.3. 可改-已初始化全局数据，放入再再高一点的.data段
##### 5.9.4. 未初始化全局数据，放入再再再高一点的.bss段，一般由程序加载者代为零初始化，即将这块区域逐字节清零
##### 5.9.5. 再再再再高一点的堆段厚放程序运行时动态分配数据，向高地址增长
##### 5.9.6. 再再再再再高一点的栈段用于函数上下文的保存与恢复，即与函数相关的局部数据或者叫局部变量会放在栈帖内，它向低地址增长，根据栈的运转特性，后进先出，其实就是所谓的现场保护。
##### 那么问题来了，堆栈相邻，堆在栈下，栈在堆上，堆向高地址增长，也就是向栈方向增长，栈向低地址增长，也就是栈向堆方向增长，栈堆会不会存在互相覆盖的情况。段保护解决此类问题。
##### 链接脚本可调整内核文件的内存布局加载物理地址
##### 5.9.7. 内核文件与内核镜像
bootloader加载内核文件时会将内核文件相关多余的元数据（大概会是些自描述环境配置之类的）丢掉，然后加载到0x80200000位置的称为内核镜像
##### 5.9.8. riscv64-unknown-elf-gdb 在arch上为 riscv64-elf-gdb
##### 5.9.9. risc-v指令
###### 5.9.9.1. rs表示源寄存器 source register
###### 5.9.9.2. imm表示立即数 immediate，是一个常数
###### 5.9.9.3. rd表示目标寄存器 destination register
###### 5.9.9.4. rs + imm 构成指令输入部分，rd是指令的输出部分
###### 5.9.9.5. rs 和 rd 可以在 32 个通用寄存器 x0~x31 中选取。但是这三个部分都不是必须的，某些指令只有一种输入类型，另一些指令则没有输出部分。
###### 5.9.9.6. 汇编伪指令 (Pseudo Instruction) ret 跳转回调用之前的位置
###### 5.9.9.7. 函数调用跳转指令----jalr指令保存返回地址并实现跳转
###### 5.9.9.7. 函数调用跳转指令----jal
###### 5.9.9.8. 被调用者保存(Callee-Saved)寄存器 
###### 5.9.9.9. 调用者保存(Call-Saved)寄存器 
###### 5.9.9.10. 开场(Prologue)结尾(Epilogue)

##### 5.9.9. risc-v通用寄存器使用约定
寄存器组a0--a7(x10--x17)，保存者为--调用者保存，用来传递输入参数。其中a0和a1还用来保存返回值
寄存器组t0--t6(x5--x7,x28--x31)，保存者为--调用者保存，做为临时寄存器使用，在被调函数中可以随意使用无需保存
寄存器组s0--s11(x8--x9,x18--x27)，保存者为--被调用者保存，作为临时寄存器使用，被调用函数保存后才能在被调用函数中使用
zero(x0)恒为零，函数调用不会对它产生影响
ra(x1)被调用者保存  
sp(x2)被调用者保存  
fp(s0)做为s0临时寄存器，做为栈帧指针(Frame Pointer)寄存器  
gp(x3)和tp(x4)在一个程序运行期间都不会变化，因此不必放在函数调用上下文中  
栈上多个fp信息实际上保存了一条完整的函数调用链
ra，sp，fp是和函数调用紧密相关的寄存器