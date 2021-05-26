# ftp 命令
***用来设置文件系统相关功能***

**补充说明**
**ftp命令**用来设置文件系统相关功能。ftp服务器在网上较为常见，Linux ftp命令功能是用命令的方式来控制在本地主机和远程主机之间传送文件，这里详细介绍Linux ftp命令的一些经常使用的命令，相信掌握了这些使用Linux进行ftp操作将会非常容易。

### 语法
```bash
ftp [选项]... [参数]...
```

### 选项
```
-d	详细显示指令执行过程，便于排错或分析程序执行的情况
-i	关闭互动模式，不询问任何问题
-g	关闭本地主机文件名称支持特殊字符的扩充特性
-n	禁用自动登录
-v	显示指令执行的详细信息
-t	激活数据包包追踪
```

### 参数
##### 主机:指定要连接的FTP服务器的主机名或IP地址

### 实例 
1> 启动ftp服务
```bash
service vsftpd start
```

2> 进入ftp提示符
```bash
/:~$ ftp
ftp>
```

3> 获得内部帮助
```bash 
ftp> help rename
```

4> 连接FTP服务器
```bash
ftp [FTP服务器或者FTP主机IP地址]
```

5> 查看ftp服务状态
```bash
service vsftpd status
```

6> 查看ftp进程
```bash
ps -aux | grep -i ftp
```
