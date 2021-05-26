# fsck 命令
***检查并且修复文件系统中的错误***

**补充说明**
**fsck命令**被用于检查并且修复文件系统中的错误。当文件系统发生错误变化，可以用fsck命令尝试加以修复

### 语法
```bash
fsck [-sACVRP] [-t fstype] [--] [fsck-options] filesys [...]
```

### 选项
```
filesys ： device 名称(eg./dev/sda1)，mount 点 (eg. / 或 /usr)
-t : 给定档案系统的型式，若在 /etc/fstab 中已有定义或 kernel 本身已支援的则不需加上此参数
-s : 依序一个一个地执行 fsck 的指令来检查
-A : 对/etc/fstab 中所有列出来的 partition 做检查
-C : 显示完整的检查进度
-d : 列印 e2fsck 的 debug 结果
-p : 同时有 -A 条件时，同时有多个 fsck 的检查一起执行
-R : 同时有 -A 条件时，省略 / 不检查
-V : 详细显示模式
-a : 如果检查有错则自动修复
-r : 如果检查有错则由使用者回答是否修复
```
### 参数
- 文件系统：指定要查看信息的文件系统

### 实例
- Linux的文件系统损坏会导致Linux不能正常关机，出错的时候如果系统告诉你是哪一块硬盘的分区有问题，比如是/dev/hda2，接着用如下的命令去对付它。
```bash
fsck -y /dev/hda2
```

- 如果不知道那里出错只需要
```bash
fsck
```

	
