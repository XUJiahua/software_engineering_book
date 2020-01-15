# Linux OOM Killer

OOM = Out of Memory。

### 现象

应用程序死得很安静，连日志都没留下。

### 原因

强杀（kill -9）进程，进程是接收不到操作系统信号的，也就是无法执行正常退出流程。

排除人为操作，99%概率，进程是被系统的OOM killer杀掉了（剩余内存紧张，谁最占用内存，谁被杀）。执行命令查看证据：

```text
dmesg -T| grep -E -i -B100 'killed process'

[四 10月 11 02:53:40 2018] 78024 pages reserved
[四 10月 11 02:53:40 2018] [ pid ]   uid  tgid total_vm      rss nr_ptes swapents oom_score_adj name
[四 10月 11 02:53:40 2018] [  490]   999   490   133560     1913      57        0             0 polkitd
[四 10月 11 02:53:40 2018] [  834]     0   834   140597     2677      96        0             0 tuned
[四 10月 11 02:53:40 2018] [23009]   997 23009   943977   277509     634        0             0 java
[四 10月 11 02:53:40 2018] [31580]  1000 31580   620066   614537    1217        0             0 coupon
[四 10月 11 02:53:40 2018] Out of memory: Kill process 31580 (coupon) score 634 or sacrifice child
[四 10月 11 02:53:40 2018] Killed process 31580 (coupon) total-vm:2480264kB, anon-rss:2458148kB, file-rss:0kB, shmem-rss:0kB
```

其中 `coupon` 是应用程序的名字。

> 内核检测到系统内存不足、挑选并杀掉某个进程的过程可以参考内核源代码 linux/mm/oom\_kill.c， 当系统内存不足的时候，out\_of\_memory\(\) 被触发，然后调用 select\_bad\_process\(\) 选择一个 “bad” 进程杀掉，如何判断和选择一个 “bad” 进程呢，总不能随机选吧？挑选的过程由 oom\_badness\(\) 决定，挑选的算法和想法都很简单很朴实：最 bad 的那个进程就是那个最占用内存的进程。
>
> [https://blog.csdn.net/wzb56\_earl/article/details/25423591](https://blog.csdn.net/wzb56_earl/article/details/25423591)

### 应对措施

1. 机器加物理内存。
2. 应用程序中，优化内存的使用。
   1. 比如，常见的内存剧烈占用，可能是一个SQL查询，返回了大量数据。建议在SQL查询中加入限制条件，大量数据分批取。



