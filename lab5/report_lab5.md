## lab5实验报告
>小组成员：杨雨奇 吴天夫 冯杰康

- copy_range函数的进行拷贝的浅拷贝深拷贝，和page2kva的转换关系。
在简单的copy_range实现的过程中使用的是深拷贝保证实现的简单化，并且使用page2kva将物理地址转化为虚拟地址进行使用memcpy进行拷贝。
如果使用copy on write则使用的是浅拷贝，并且需要设置页表项相应的写权限的相关权限，在写操作触发pagefault的时候进行深拷贝，这样做可以节约内存并且防止数据冲突。

- 不同的进程拥有自己的栈空间，用来存储自己的变量。

- do_wait函数中会清理其子节点中的僵尸状态的进程的pcb，这样一个进程就彻底从内存中清除；如果当前的子进程没有全部结束，则自己进入睡眠状态，等待子进程调用do_exit时唤醒自己，清理子进程pcb，直到子进程全部结束，自己也可以进入退出状态。

- do_exit会将自己的子进程全部接在initproc进程上，因为initproc一直出于do_wait的状态，因此可以当其帮助清理子进程的pcb。

- do_fork进行之后，在父进程的唤醒下会进行do_exec操作，此时可能清理fork时的页表。

- 在init_main中调用kernel_thread函数创建了user_main线程，并且唤醒，并且user_main触发可exec中断（系统调用），中断里调用do_exec()，之后在do_exec()函数中进程了mm的清空（这里因为之前fork了一个进程，因此它的mm暂时是fork前的，即父进程的，如果这个页表没有用是可以清掉的，之后建立存放自己数据的页表），并且设置中断帧中的eip和esp等，之后使用iret就可以直接跳到子进程的eip处进行执行。

- pid的数量根据pcb的大小和内核空间的大小决定，不能运行太多的进程导致内核空间被占满。