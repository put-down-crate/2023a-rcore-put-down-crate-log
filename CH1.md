# 2023a-rcore-put-down-crate-log
2023年秋冬季开源操作系统训练营第二阶段的日常学习记录

## CH1 祖传Hello, World！  

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
#### 4.2. 实现彩色输出宏 (只要求可以彩色输出，不要求 log 等级控制，不要求多种颜色)
#### 4.3. 隐形要求
可以关闭内核所有输出。从 lab2 开始要求关闭内核所有输出（如果实现了 log 等级控制，那么这一点自然就实现了）。
#### 4.4.利用彩色输出宏输出 os 内存空间布局
输出 .text、.data、.rodata、.bss 各段位置，输出等级为 INFO。
#### 4.5. challenge: 支持多核，实现多个核的 boot


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