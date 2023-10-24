# 2023a-rcore-put-down-crate-log
2023年秋冬季开源操作系统训练营第二阶段的日常学习记录

## CH0 实验环境配置注意事项
### 1.实验模拟环境选择
由于本人对Arch常规使用较为熟悉，所以选择了，物理机Arch & WSL2Arch 这样的配合，通常情况使用WSL2Arch，如果实验时存在未知情况，那么物理机Arch做为备用环境。  
*WSL2使用了WIN自带的VM组件，这会导致与vmware等非WIN的VM之间的冲突
### 2.实验环境的国内镜像相关配置

为了便于实验环境的更加顺畅的构建与使用，尽量避免鬼打墙的情况，所以有必要使用相关配置的国内镜像，本人泛用tuna，sjtu，ustc。  
  
#### 2.1. arch pacman mirror 配置:  
使用命令  
``$ vim /etc/pacman.conf``  
打开pacman配置文件  
追加  
``
[archlinuxcn]
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
``  
到文件末尾
  
#### 2.2. qemu & riscv64 安装  
``
$ sudo pacman -S qemu-full # version : 8.1.2-1
``  
``
$ sudo pacman -S riscv64-elf-binutils # vesion :  2.39-1
``  
``
$ sudo pacman -S riscv64-elf-gcc # version :  12.2.0-1
``  
``
$ sudo pacman -S riscv64-elf-gdb # version : 13.1-2
``  
``
$ sudo pacman -S riscv64-elf-newlib # version : 4.1.0-3
``   
*其中 riscv64-elf-newlib 的安装描述为 "A C standard library implementation intended for use on embedded systems (RISCV64 bare metal)"，不知道有没有用，先装上。  

#### 2.3. rustup & cargo 镜像配置  

##### 2.3.1. rustup 镜像配置
使用命令  
``
$ vim ~/.bashrc
``  
打开本地用户bash配置文件，并追加  
``
export RUSTUP_DIST_SERVER=https://mirrors.ustc.edu.cn/rust-static``  
``export RUSTUP_UPDATE_ROOT=https://mirrors.ustc.edu.cn/rust-static/rustup``  
到文件.bashrc末尾


##### 2.3.2. cargo 镜像配置  
使用命令  
``
$ vim ~/.cargo/config  
``
开打cargo配置文件，并使用如下配置替换填充config内容:  
````
[source.crates-io]
registry = "https://github.com/rust-lang/crates.io-index"
#replace-with = 'tuna'
#replace-with = 'sjtu'
replace-with = 'ustc' 

[source.tuna]
registry = "https://mirrors.tuna.tsinghua.edu.cn/git/crates.io-index.git"

[source.ustc]
registry = "git://mirrors.ustc.edu.cn/crates.io-index"

[source.sjtu]
registry = "https://mirrors.sjtug.sjtu.edu.cn/git/crates.io-index"

[source.rustcc]
registry = "git://crates.rustcc.cn/crates.io-index"
````

#### 2.4. rustup 安装组件及配置  
  
``$ rustup default nightly``  
``$ rustup target add riscv64gc-unknown-none-elf``  
``$ rustup component add llvm-tools-x86_64-unknown-linux-gnu``  
``$ rustup component add rust-docs-x86_64-unknown-linux-gnu``  
``$ rustup component add rust-src``  
``$ rustup component add rust-std-riscv64gc-unknown-none-elf``  

### 3. 验证环境配置  
使用命令  
``
$ git clone https://github.com/rcore-os/rCore-Tutorial-v3.git
``  
将rCore原始项目克隆到本地，  
使用命令  
``$ cd rCore-Tutorial-v3``  
``$ cd os``  
``$ make run``  
编译运行出现：  
````
[rustsbi] RustSBI version 0.3.1, adapting to RISC-V SBI v1.0.0
.______       __    __      _______.___________.  _______..______   __
|   _  \     |  |  |  |    /       |           | /       ||   _  \ |  |
|  |_)  |    |  |  |  |   |   (----`---|  |----`|   (----`|  |_)  ||  |
|      /     |  |  |  |    \   \       |  |      \   \    |   _  < |  |
|  |\  \----.|  `--'  |.----)   |      |  |  .----)   |   |  |_)  ||  |
| _| `._____| \______/ |_______/       |__|  |_______/    |______/ |__|
[rustsbi] Implementation     : RustSBI-QEMU Version 0.2.0-alpha.2
[rustsbi] Platform Name      : riscv-virtio,qemu
[rustsbi] Platform SMP       : 1
[rustsbi] Platform Memory    : 0x80000000..0x88000000
[rustsbi] Boot HART          : 0
[rustsbi] Device Tree Region : 0x87e00000..0x87e010de
[rustsbi] Firmware Address   : 0x80000000
[rustsbi] Supervisor Address : 0x80200000
[rustsbi] pmp01: 0x00000000..0x80000000 (-wr)
[rustsbi] pmp02: 0x80000000..0x80200000 (---)
[rustsbi] pmp03: 0x80200000..0x88000000 (xwr)
[rustsbi] pmp04: 0x88000000..0x00000000 (-wr)
KERN: init gpu
KERN: init keyboard
KERN: init mouse
KERN: init trap
/**** APPS ****
barrier_fail
forktest
priv_csr
sync_sem
pipe_large_test
eisenberg
mpsc_sem
peterson
store_fault
huge_write
adder_peterson_spin
exit
race_adder_arg
count_lines
stackless_coroutine
gui_uart
usertests
yield
initproc
threads_arg
pipetest
infloop
fantastic_text
stackful_coroutine
cat
random_num
gui_simple
run_pipe_test
barrier_condvar
stack_overflow
adder_simple_yield
priv_inst
adder
hello_world
condsync_sem
forktest_simple
gui_snake
gui_rect
sleep
udp
adder_peterson_yield
cmdline_args
inputdev_event
condsync_condvar
forktest2
sleep_simple
adder_mutex_blocking
phil_din_mutex
user_shell
forktree
adder_mutex_spin
huge_write_mt
adder_atomic
adder_simple_spin
filetest_simple
until_timeout
threads
tcp_simplehttp
matrix
**************/
Rust user shell
>>
````

### 4. 其它相关情况
#### 4.1. 实验说明书  
给出的地址打不开 https://rcore-os.cn/rCore-Tutorial-Book-v3/index.html  
gitee的版本可以打开 http://rcore-os.gitee.io/rcore-tutorial-book-v3/index.html
#### 4.2. 使用github desktop  
   clone & push 时相对顺畅些，也要多try几次。

### 5. 填文档是比较累，但确实是收拾思绪总结经验好办法。   