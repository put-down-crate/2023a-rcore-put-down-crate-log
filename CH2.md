# 2023a-rcore-put-down-crate-log
2023年秋冬季开源操作系统训练营第二阶段的日常学习记录

## CH2 批处理系统
## 总结：
一：****

### 1. 切换ch2分支，运行rCore，发现问题
使用命令  
``$ cd 2023a-rcore-put-down-crate``  
``$ git checkout ch2``  
``$ cd os``  
``$ make run``   
出现如下错误提示：  
````
make[1]: Entering directory '/home/homi/site-root/2023a-rcore-put-down-crate/os'
make[1]: *** ../user: No such file or directory.  Stop.
make[1]: Leaving directory '/home/homi/site-root/2023a-rcore-put-down-crate/os'
make: *** [Makefile:44: kernel] Error 2
````  
提示 ``2023a-rcore-put-down-crate/user`` 不存在
参考 ``2023a-rcore-put-down-crate/README.md`` 需进行如下操作:  
````
$ cd 2023a-rcore-put-down-crate
$ git clone https://github.com/LearningOS/rCore-Tutorial-Test-2023A.git user
$ cd os
$ git checkout ch2
$ make run
````  
编译后光标闪烁无输出，但按下ctrl+a x有提示  
``QEMU: Terminated``  
并退出回到命令行提示状态，QEMU没问题。

### 2.本章关注点
#### 2.1. 连接核心与用户应用
##### 2.1.1. 把应用程序执行文件从 ELF 执行文件格式变成 Binary 格式（通过 rust-objcopy 可以轻松完成）
##### 2.1.2. 然后这些 Binary 格式的文件通过编译器辅助脚本 os/build.rs 转变变成 os/src/link_app.S 这个汇编文件的一部分，并生成各个 Binary 应用的辅助信息，便于操作系统能够找到应用的位置。编译器会把操作系统的源码和 os/src/link_app.S 合在一起，编译出操作系统 +Binary 应用的 ELF 执行文件，并进一步转变成 Binary 格式。
##### 2.1.3. 为了定位 Binary 应用在被加载后的内存位置，操作系统本身需要完成对 Binary 应用的位置查找，找到后（通过 os/src/link_app.S 中的变量和标号信息完成），会把 Binary 应用从加载位置拷贝到 user/src/linker.ld 指定的物理内存位置（OS 的加载应用功能）。
##### 2.1.4. 在一个应用执行完毕后，操作系统还能加载另外一个应用，这主要是通过 AppManagerInner 数据结构和对应的函数 load_app 和 run_next_app 等来完成对应用的一系列管理功能。
##### 2.1.5. 简单总结
##### 编译过程：
APP-ELF文件 ->  
|rust-objcopy| ->   
binary文件 ->  
|os/build.rs| ->  
os/src/link_app.S ->  
与OS源码合并编译 ->  
OS+BinaryAppELF
  
##### 加载过程：
OS ->  
load BinaryApp (find info from os/src/link_app.S) ->  
copy BinaryApp ->  
(user/src/linker.ld) 指定的物理地址

##### 下一个用户应用:
AppManagerInner->  
load_app->  
run_next_app
AppManagerInner大概应该具有枚举能力  

#### 2.2. 软硬件协同设计的特权级 
##### 2.2.1. 对应用程序的限制
 2.2.1.1. 应用程序不能访问任意的地址空间  
 2.2.1.2. 应用程序不能执行某些可能破坏计算机系统的指令  
##### 2.2.2. 安全的指令相关
2.2.2.1 传统函数调用方式直接绕过硬件特权级保护检查(含call、ret的指令及指令组)   
2.2.2.2新设计指令
#### ecall 执行环境调用
具有``用户态到内核态``的执行环境切换能力的``函数调用``指令
#### eret 执行环境返回
具有``内核态到用户态``的执行环境切换能力的``函数返回``指令


##### 2.2.3. RISC-V特权级架构
级别0--编码00--名称用户/应用模式(U, User/Application)  
级别1--编码01--监督模式(S, Supervisor)  
级别2--编码10--虚拟监督模式(H, Hypervisor)  
级别3--编码11--机器模式(M, Machine)  
M模式最高特权级，U模式最低特权级，硬件层面M模式必须存在，其它可以不存在

U模式：应用程序--简单嵌入式  
S模式：OS内核  
M模式：bootloader  
混合模式：
M/U：带有保护能力的嵌入式系统
M/S/U：复杂多任务模式
##### H模式特权规范还没有完全制定好，没用


##### 2.2.4. 控制流
常规控制流：顺序-循环-分支-函数调用  
异常控制流：ECF，Exception Control FLow 伴随下层对上层的监控管理与特权级别切换  

##### 2.2.4.1. RISC-V 异常一览
ExceptionCode 0--Instruction address misaligned  
ExceptionCode 1--Instruction access fault  
ExceptionCode 2--Illegal instruction  
ExceptionCode 3--Breakpoint  
ExceptionCode 4--Load address misaligned
ExceptionCode 5--Load access fault
ExceptionCode 6--Store/AMO address misaligned
ExceptionCode 7--Store/AMO access fault
ExceptionCode 8--Environment call from U-Mode
ExceptionCode 9--Environment call from S-Mode
ExceptionCode10--Reserved
ExceptionCode11--Environment call from M-mode
ExceptionCode12--Instruction page fault  
ExceptionCode13--Load page fault
ExceptionCode14--Reserved
ExceptionCode15--Store/AMO page fault

##### 2.2.4.2. RISC-V S模式特权指令 
sret--从S模式返回U模式，在U模式下执行会产生非法指令异常
wfi--处理器在空闲时进入低功耗状态等待中断：在U模式下执行会产生非法指令异常
sfence.vma--刷新TLB缓存：在U模式下执行会产生非法指令异常
访问S模式CSR的指令--通过访问sepc/stvec/scause/sscartch/stval/sstatus/satp 等CSR来改变系统状态：在U模式下执行会产生非法指令异常

#### 2.2.5. 应用程序设计实现要点
##### 2.2.5.1. 应用程序的内存布局
##### 2.2.5.2. 应用程序发出的系统调用