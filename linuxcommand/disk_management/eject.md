# eject 命令
***用来退出抽取式设备***

**补充说明**
**eject命令**用来退出抽取式设备。若设备已挂入，则eject命令会先将该设备卸除再退出

### 语法
```bash
eject [-dfhnqrstv][-a <开关>][-c <光驱编号>][设备]
```

### 选项
```
[设备] 设备可以是驱动程序名称，也可以是挂入点。
-a<开关>, --auto<开关> 控制设备的自动退出功能。
-c<光驱编号>, --changerslut<光驱编号> 选择光驱柜中的光驱。
-d, --default 显示预设的设备，而不是实际执行动作。
-f, --floppy 退出抽取式磁盘。
-h, --help 显示帮助。
-n, --noop 显示指定的设备。
-q, --tape 退出磁带。
-r, --cdrom 退出光盘。
-s, --scsi 以SCSI指令来退出设备。
-t, --trayclose 关闭光盘的托盘。
-v, --verbose 执行时，显示详细的说明。
```
### 参数
- 设备名：指定弹出的设备名称