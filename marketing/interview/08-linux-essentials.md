# Linux 性能分析基础 · 面经

## 核心问题
1. 系统变慢了怎么排查？常用的性能分析工具有哪些？分哪几层看？
2. CPU 高、IO 高、网络丢包分别怎么定位到具体进程？
3. top 里的 load average 是什么意思？wa 高说明什么？

## 答题要点
- **性能分析分层框架**（Brendan Gregg 体系）：
  - 系统层：top/vmstat/uptime 看整体负载。
  - 进程层：ps/pidstat 看进程资源占用。
  - CPU：top 看 us/sy/wa/si 比例，pidstat -u 看进程 CPU，perf top 看热点函数。
  - 内存：free -h 看 available（不是 free），vmstat 看 si/so 交换，pidstat -r 看进程 RSS。
  - IO：iostat -x 看 await/%util，iotop 看进程 IO，dstat 综合看。
  - 网络：ss -s 看连接汇总，ss -tan 看 TCP 状态，tcpdump 抓包，sar -n DEV 看历史流量。
- **load average 含义**：1/5/15 分钟内 R 状态（运行+运行队列等待）进程数的指数移动平均，包括等待 IO 的 D 状态进程。CPU 核数是基准线，8 核机器 load 持续 >8 说明过载。wa（iowait）高说明 CPU 在等 IO，根因在磁盘而非 CPU。
- **进程状态**：R（运行/就绪）、S（可中断睡眠，等事件）、D（不可中断睡眠，通常等 IO，无法 kill）、Z（僵尸，父进程未回收）、T（停止）。
- **CPU 高定位**：top 找出 CPU 高的进程 -> `pidstat -u 1` 确认 -> `perf top -p <pid>` 看热点函数 -> 火焰图（perf record + FlameGraph）定位代码。
- **IO 高定位**：iostat -x 1 看 await（>20ms 偏慢）和 %util（接近 100% 饱和）-> iotop 找 IO 大户 -> `strace -p <pid> -e trace=read,write` 看系统调用。
- **网络丢包定位**：`ss -s` 看连接异常 -> `netstat -s | grep -i retrans` 看重传 -> `tcpdump -i eth0 port 80 -w out.pcap` 抓包 -> `ethtool -S eth0 | grep -i drop` 看网卡丢包 -> `tc -s qdisc show` 看队列。
- **内存看 available 而非 free**：buffer/cache 是可回收的，free 小不代表内存紧张。OOM 看 `dmesg | grep -i oom` 找被杀进程与内存水位。

## 加分回答
Linux 性能分析的核心方法论是"自顶向下，逐层下钻"。先用全局工具（top/vmstat/dstat）看清整体瓶颈在哪类资源（CPU/内存/IO/网络），再用进程级工具（pidstat/iotop/ss）定位到具体进程，最后用系统调用级工具（strace/perf/ebpf）定位到代码。切忌一上来就 strace，会陷在细节里出不来。Brendan Gregg 的 USE 方法（资源：使用率/饱和度/错误）和 ROS 方法（资源：运行队列/饱和度/错误）是结构化分析的利器，每个资源都问这三个维度。

容器场景下有个经典坑：`/proc` 看到的是宿主机数据，不是容器的。top 在容器里看到的 CPU/内存是宿主机的，会误判。正确做法是从 cgroup 读取容器真实用量：CPU 看 `/sys/fs/cgroup/cpu/cpuacct.usage`，内存看 `/sys/fs/cgroup/memory/memory.usage_in_bytes`。生产建议直接用 cAdvisor 或 node-exporter + cAdvisor exporter 暴露容器指标，别在容器里跑 top。另一个进阶工具是 eBPF（bcc/bpftrace），能在内核态低开销地追踪系统调用、网络包、函数调用，是下一代性能分析的方向，相比 strace 的全局开销小得多。

## 口播版短文案
Linux 性能排查记住一个框架：自顶向下逐层下钻。先 top 看整体负载，再 pidstat 定位进程，最后 perf 或 strace 定位代码。top 里三个数 load average 是 1、5、15 分钟的运行队列长度，超过 CPU 核数就是过载，wa 高说明 CPU 在等 IO 不是 CPU 不够用。CPU 高用 perf top 看热点函数，配火焰图一目了然。IO 高先 iostat 看 await 和 util，再 iotop 找大户。网络丢包 ss 看连接，tcpdump 抓包，ethtool 看网卡。最后提醒一个容器大坑：容器里 top 看到的是宿主机数据，要看 cgroup 才准，别被误导了。

## 标签
Linux, 性能分析, CPU, IO, 网络, perf, 火焰图, 运维面试
