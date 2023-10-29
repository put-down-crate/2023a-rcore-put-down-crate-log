# 2023a-rcore-put-down-crate-log
2023年秋冬季开源操作系统训练营第二阶段的日常学习记录

## CH1 批处理系统
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
