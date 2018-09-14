---
title: Java-Note-通过相对位置读取文件
date: 2018-04-23 19:42:24
tags:
category: Java
---
- 项目代码中:
    - 想要读取项目下的文件, 又不希望写死路径, 可以把路径写为: "src/..."
- 浏览器中: 浏览器中读取服务器的文件
    - 在tomcat的service.xml进行一下修改(Context标签):
    ```
    <Host name="localhost"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">
         <Context path="/custom_api/static"  docBase="\custom_api\WEB-INF\static" 
                       reloadable="true" debug="0"/>
         <Context path="/custom_api"  docBase="D:\runtime\jnScience" 
                       reloadable="true" debug="0"/>
        <!-- SingleSignOn valve, share authentication between web applications
             Documentation at: /docs/config/valve.html -->
        <!--
        <Valve className="org.apache.catalina.authenticator.SingleSignOn" />
        -->

        <!-- Access log processes all example.
             Documentation at: /docs/config/valve.html
             Note: The pattern used is equivalent to using pattern="common" -->
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log" suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />
    </Host>
    ```
