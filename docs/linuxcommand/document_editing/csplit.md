# csplit 命令
***将一个大文件分割成小的碎片文件***

**补充说明**
**csplit命令**用于将一个大文件分割成小的碎片，并且将分割后的每一个碎片保存成一个文件。碎片文件的命令类似"xx00"，"xx01"。csplit命令你个是split的一个变体，split只能够根据文件大小或行数来分割，但csplit命令能够根本文件的本身特点来分割文件。

### 语法
```bash
csplit [选项]... [参数]...
```

### 选项
```
-b, --suffix-format=FORMAT	预设的输出格式其文件名称为"xx00","xx01"等，用户可以通过改变<输出格式>来改变输出的文件名
-f, --prefix=PREFIX		预设的输出首字符串其文件明为"xx00","xx01"，如果指定输出是子首字符串为"hello".则输出的文件名称就会变成"hello00","hello01"
-k, --keep-files		保留文件，就算发生错误或中断执行，与不能删除已经输出保存的文件
    --suppress-matched	
-n, --digits=数位             使用指定的进制数位代替二进制
-s, --quiet, --silent 不显示输出文件的尺寸计数
-z, --elide-empty-files       删除空的输出文件
      --help            显示此帮助信息并退出
      --version         显示版本信息并退出

```

### 参数
##### 文件：指定要分割的原文件
##### 模式：指定要分割文件时的匹配模式

### 实例
1> 将文本文件filename以第2行为分界点切割成两份
```bash
csplit filename 2
```


2> 将文本文件filename以120行为分界点切割2份，并指定输出文件名的位数为3
```bash
csplit -n 3 filename 120
```

3> 将文本文件testfile以120为分界点切割2份，并制定文件名的前缀为fileName位数3
```bash
csplit fileName testfile 120
```

4> 将文件filename以10行为单位分割8次，并指定输出文件名的格式
```bash
csplit -b "myfile%o%" filename 10 {8}
```
