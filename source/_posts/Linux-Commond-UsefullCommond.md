---
title: Linux-Commond-UsefullCommond
date: 2019-03-30 12:27:56
tags: Linux
category: Linux
---
1. top
   1. 显示包含指定command string的线程信息并定时刷新(使用bash)
      ```shell
      __keyword=name_of_process; (while :; do __arg=$(pgrep -d',' -f $__keyword); if [ -z "$__arg" ]; then top -u 65536 -n 1; else top -c -n 1 -p $__arg; fi; uptime; sleep 1; done;)
      ```
2. strace
   1. 追踪目标命令并显示时间和地址及将输出结果放入指定文件
      ```shell
      $ strace -tt -i -o outputfile java -version
      ```
3. scp: 远程文件传输
   1. 将文件传送给指定的远程机器
      ```shell
      $ scp websophic.20190313.v1.3.2.zip ems@198.87.104.101:/users/ems/
      ```
4. tar
   1. tar解压tar.gz文件
      ```shell
      $ tar -xvzf target.tar.gz
      ```