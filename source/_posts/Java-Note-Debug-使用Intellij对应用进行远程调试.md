---
title: Java-Note-Debug-使用Intellij对应用进行远程调试
date: 2018-05-11 16:37:13
tags: Debug
category: Java
---
- 环境配置
    - 远程服务器
        - 操作系统: centos 6.5
        - tomcat版本: 8.0
- 步骤
    1. 打开端口8000(如果是云服务器还需打开8000端口的入站流量)
    1. 修改远程tomcat服务器配置: 使用catalina.sh代替startup.sh来启动
        1. 关闭远程tomcat: 
        ```
        cd /var/tomcat/tomcat-8/bin
        ./shutdown.sh
        ```
        2. 修改catalina.sh中的代码, 将
        ```
        if [ "$1" = "jpda" ] ; then
            if [ -z "$JPDA_TRANSPORT" ]; then
                JPDA_TRANSPORT="dt_socket"
            fi
            if [ -z "$JPDA_ADDRESS" ]; then
                JPDA_ADDRESS="localhost:8000"
            fi
            if [ -z "$JPDA_SUSPEND" ]; then
                JPDA_SUSPEND="n"
            fi
            if [ -z "$JPDA_OPTS" ]; then
                JPDA_OPTS="-agentlib:jdwp=transport=$JPDA_TRANSPORT,address=$JPDA_ADDRESS,server=y,suspend=$JPDA_SUSPEND"
            fi
            CATALINA_OPTS="$CATALINA_OPTS $JPDA_OPTS"
            shift
        fi
        ```
        中的`JPDA_ADDRESS="localhost:8000"`改为`JPDA_ADDRESS="0.0.0.0:8000"`. 因为0.0.0.0表示所有的ip地址都可以访问, `JPDA`是指`Java Platform Debugger Architecture`, 是一种专门用于Java调试的架构: [JPDA](https://docs.oracle.com/javase/8/docs/technotes/guides/jpda/).
        3. 重启tomcat: `./catalina.sh jpda start`
    1. 修改本地Intellij配置
        1. 在`Edit Configuration`中增加一个`Remote`, 将里面的Port改为8000, 保存然后点击debug按钮
        2. 当出现`Connected to the target VM, address: '140.143.29.99:8000', transport: 'socket'`时表示远程连接调试端口成功, 可以开始调试
- 注意: 在步骤3的1中, 如果出现`Unable to open debugger port (x.x.x.x:8000): java.net.ConnectException "Connection refused: connect"`说明调试端口为对客户端开放, 此时可以在远程服务器上使用`netstat -ntlp`查看开放的端口, 有可能没有开放8000端口或者开放的是`127.0.0.1:8000`而不是`0.0.0.0:8000`

