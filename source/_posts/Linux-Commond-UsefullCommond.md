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
5. tcpdump
   1. 获取指定网卡指定主机的包数据并存入指定文件中
      ```shell
      tcpdump -i ens192 host 192.168.254.200 -w cap190715_2009.pcap
      ```
6. grep
   1. 在文件夹中查找字符串
      ```shell
      grep -rnw '/path/to/somewhere/' -e 'pattern'
      ```
7. sed
   1. 获取文本中指定行范围
      ```shell
      sed -n 5,8p file
      ```
8. dd
   1. 检测磁盘速度
      ```shell
      dd if=/dev/zero of=/tmp/output conv=fdatasync bs=384k count=1k; rm -f /tmp/output
      ```

9. java remote debug
      ```shell
      /usr/local/jdk1.8.0_144/bin/java -jar -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=12543 -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.port=20250 -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false -Djava.rmi.server.hostname=192.168.255.205 -Dspring.profiles.active=test cms_web-0.0.1-SNAPSHOT.war
      ```

10. tail高亮
      ```shell
      tail -f 日志文件 |grep -E “高亮的关键字1|高亮的关键字2|…|高亮的关键字N” -A10 -B10 --color=auto
      ```

11. docker命令
    1.  `docker exec`: 在docker中执行bash命令
        1. `docker exec -it CONTAINER_NAME bash`: 在docker中新建一个bash
        2. `docker exec -it CONTAINER_NAME jstack PID > threadDump.log`: 将thread dump保存到指定文件中
        3. `sudo docker cp e4e2ffe62ca0:arthas-boot.jar /tmp/`: 获取容器中的文件
12. top命令
    1.  `top -Hp pid`: 显示一个进程的线程运行信息列表
