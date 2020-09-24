# comm 命令
***两个文件之间的比较***

**补充说明**

**comm命令**可以用于两个文件之间的比较，它有一些选项可以用来调整输出，以便执行交际、求差、以及查集操作。

### 语法
```bash
comm [选项]... [参数]...
```

### 选项
```
-1	不显示在第一个文件出来的内容
-2	不显示在第二个文件出来的内容
-3	不显示同在两个文件中都出现的内容
```

### 参数

##### 文件1：指定要比较的第一个有序文件
##### 文件2：指定要比较的第二个有序文件

### 实例
1> aaa.txt 与 bbb.txt 的文件内容如下
```
[root@localhost text]# cat aaa.txt 
aaa 
bbb 
ccc 
ddd 
eee 
111 
222
[root@localhost text]# cat bbb.txt
bbb 
ccc 
aaa 
hhh 
ttt 
jjj
```
执行 comm 命令输出结果如下
```
[root@localhost text]# comm aaa.txt bbb.txt 
aaa
                bbb
                ccc
        aaa
ddd
eee
111
222
        hhh
        ttt
        jjj
第一列  第二列  第三列
```
输出的第一列只包含在 aaa.txt 中出现的列，第二列包含在 bbb.txt 中出现的列，第三列包含在 aaa.txt 和 bbb.txt 中都包含的列。各列是以制表符
