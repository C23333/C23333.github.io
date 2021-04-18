# AS 开发问题Unable to start adb server: error: protocol fault (couldn't read status): Connection reset by peer

**此错误另一种报错方式远程主机强迫关闭了一个现有的连接?**

 ## 情况出现：

* 打开androidstudio，一直连接不上电脑，提示：Unable to start adb server: error: protocol fault (couldn't read status): Connection reset by peer
  问题原因：
  大多数情况是5037端口被占用。5037为adb默认端口。
  解决办法：
  查看哪个程序占用了adb端口，结束这个程序，然后重启adb就好了。

1. 使用命令：netstat -aon|findstr "5037"  找到占用5037端口的进程PID。

![20170613095944330](https://lalalademaxiya01.oss-cn-beijing.aliyuncs.com/img20200711232007.png)



2. 使用命令：tasklist|findstr "5440"  通过PID找出进程。

![20170613100237440](https://lalalademaxiya01.oss-cn-beijing.aliyuncs.com/img20200711232020.png)

3. 调出任务管理器，找到这个进程，结束进程。
4. 使用命令:adb start-server 启动adb就行了 （或者重新编译运行也可自动启动adb）