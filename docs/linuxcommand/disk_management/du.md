# du 命令
***显示每个文件和目录的磁盘使用空间***

**补充说明**
**du命令**也是查看使用空间的，但是和df命令不同的是Linux du命令是对文件和目录磁盘使用的空间的查看，还是和df命令有一些区别的

### 语法
```bash
du [选项]... [参数]...
```

### 选项
```
-0，--null以NUL而不是换行符结束每个输出行
-a，-all （不仅是目录）的所有写入计数
   --apparent-size打印外观大小，而不是磁盘使用情况；虽然
                          表观尺寸通常较小，可能是
                          由于（'稀疏'）文件中的孔较大，内部
                          碎片，间接块等
-B，--block-size = SIZE按SIZE缩放比例大小，然后再打印；例如：'-BM'以1,048,576字节为单位打印大小；请参阅下面的SIZE格式
  -b，--bytes相当于'--apparent-size --block-size = 1'
  -c，-总计产生总计
  -D，--dereference-args 仅取消引用列出的符号链接命令行
  -d，--max-depth = N	打印目录（或文件，使用--all）的总数
                          仅在命令以下为N个或更少级别时
                          行参数--max-depth = 0与
                           - 总结
      --files0-from = F		总结磁盘的使用情况
                         	 文件F中指定的NUL终止文件名；
                          	如果F为-，则从标准输入中读取名称
  -H	等效于--dereference-args（-D）
  -h，--human-readable		人类可读格式的人类可读打印尺寸（例如1K 234M 2G）
      --inodes	列出索引节点使用情况信息，而不是块使用情况
  -k,--block-size = 1K
  -L，--dereference	取消引用所有符号链接
  -l，--count-links	如果硬链接，则计数很多次
  -m,--block-size = 1M
  -P，--no-dereference	不遵循任何符号链接（这是默认设置）
  -S，--separate-dirs	目录不包括子目录的大小
      --si	类似于-h，但使用1000的幂而不是1024
  -s，-summarize仅显示每个参数的总计
  -t，--threshold = SIZE	如果为正则排除小于SIZE的条目，
                        	  或大于负数的条目（如果为负）
      --time显示文件中最后一个文件的修改时间
                          目录或其任何子目录
      --time = WORD	将时间显示为WORD而不是修改时间：
                          时间，访问，使用，时间或状态
      --time-style = STYLE	使用STYLE显示时间，可以是：
                          	  全iso，long iso，iso或+ FORMAT；
                           	 格式的解释类似于“日期”
  -X，--exclude-from = FILE	排除与FILE中任何模式匹配的文件
      --exclude = PATTERN	排除与PATTERN匹配的文件
  -x，--one-file-system		跳过不同文件系统上的目录
      --help显示此帮助信息并退出
      --version显示版本信息并退出
```
### 参数
- 文件

### 实例
1> 显示目录或者文件所占空间
```bash
du
```

2> 查看指定文件所占空间
```bash
du log2020.log
```

3> 查看指定目录所占空间
```bash
du docs
```

4> 显示多个文件所占空间
```bash
du docs.tar.gz
```

5> 只显示综合的大小
```bash
du -s
```
