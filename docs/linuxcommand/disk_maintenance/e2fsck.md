# e2fsck 命令
***用于检查第二扩展文件系统的完整性***


**补充说明**
**e2fsck命令**用于检查第二扩展文件系统的完整性，通过适当的选项可以尝试修复出现的错误。

### 语法
```bash
e2fsck [-pacnydfvFV] [-b superblock] [-B blocksize] [-l|-L bad_blocks_file] [-C fd] device
```
### 选项
```
device ： 预备检查的硬盘 partition，例如：/dev/sda1
-a : 对 partition 做检查，若有问题便自动修复，等同 -p 的功能
-b : 设定存放 superblock 的位置
-B : 设定单位 block 的大小
-c : 检查该partition 是否有坏轨
-C file : 将检查的结果存到 file 中以便查看
-d : 列印 e2fsck 的 debug 结果
-f : 强制检查
-F : 在开始检查前，将device 的 buffer cache 清空，避免有错误发生
-l bad_blocks_file : 将有坏轨的block资料加到 bad_blocks_file 里面
-L bad_blocks_file : 设定坏轨的block资料存到 bad_blocks_file 里面，若无该档则自动产生
-n : 将档案系统以[唯读]方式开启
-p : 对 partition 做检查，若有问题便自动修复
-v : 详细显示模式
-V : 显示出目前 e2fsck 的版本
-y : 预先设定所有检查时的问题均回答[是]=
```

### 实例
- 检查/dev/sda1是否有问题，如果发现问题便自动修复
```bash
e2fsck -a -y /dev/sda1
```
- 注意
执行e2fsck或者fsck前请先umount partition，否则有机会令档案系统损毁。如果需要对根目录/进行检查以及修复，便需要进入singal user mode执行
