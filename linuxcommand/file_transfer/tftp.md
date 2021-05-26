# tftp 命令
***在本机和tftp服务器之间使用TFTP协议传输文件***

**补充说明**
**tftp命令**用在本季和tftp服务器之间使用TFTP协议传输文件。TFTP是用来下载远程文件的最简单网络协议，它其与UDP协议而实现。嵌入式linux的tftp开发环境包括两个方面：一是linux服务器端的tftp-server支持，二是嵌入式目标系统的tftp=client支持。因为u-boot本身内置支持tftp-client，所以嵌入式目标端就不用配置了。

### 语法
```bash
tftp [选项]... [参数]...
```

### 选项
```
-c	指定与tftp服务器连接成功后，立即要执行的指令
-m	指定文件传输模式。可以使ASCII或者Binary
-v	显示指令详细执行过程
-V	显示指令版本信息
```

### 参数
- 主机：指定tftp要联机的tftp服务器的ip地址或主机名

### 实例
#### 1 > 安装tftp服务器
需要安装xinetd、tftp、tftp-server 3个软件，通过yum安装
```bash
yum install xinetd
yum install tftp
yum install tftp-server
```
#### 2> 配置tftp服务器
修改/etc/xinetd.d/tftp文件，将其中的disable=yes改成disable=no。主要是设置TFTP服务器的跟目录，开启服务。修改后的文件如下:
```
server tftp
{
	socket_type=dgram
	protocol=udp
	wait=yes
	user=root
	server=/usr/sbin/in.tftpd
	server_args=-s /home/mike/tftpboot -c
	disable=no
	per_source=11
	cps=100 2
	flags=IPv4
}
```
修改项sever_arga=-s <path> -c, 其中<path>可以改为你的tftp-server的根目录，参数-s指定chroot，-c指定了可以创建文件。

#### 3> 启动tftp服务器并关闭防火墙
```bash
/etc/init.d/iptable stop 
sudo /sbin/server xined start

server xined restart
/etc/init.d/xined start
```
看到启动[OK]就可以了

#### 4> 查看tftp服务是否开启
```bash
netstat -a | grep tftp
```
#### 5> tftp使用
连接远程服务器"218.28.188.288"
```bash
tftp 218.28.188.288
```
远程下载file文件
```bash
tftp> get file
```
退出tftp
```bash
tftp> quit
```
#### 6> tftp命令的用法
```bash
tftp your-ip-address
```

进入TFTP操作
- connect	连接到远程tftp服务器
- mode		文件传输模式
- put		上传文件
- get		下载文件
- quit		退出
- verbose	显示详细的处理信息
- tarce		显示包路径
- staus		显示当前状态信息
- binary	二进制文件传输模式
- ascii		ascii传送模式
- rexmt		设置包传输的超时时间
- timeout	设置重传的超时时间
- help		帮助信息
- ?		帮助信息


