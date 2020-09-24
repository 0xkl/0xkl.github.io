# egrep 命令
***在文件内查找指定的字符窗***

**补充说明**
**egrep命令**用于在文件内查找指定的字符串。egrep执行效果与grep -E 相似，使用的语法己参数可参照grep命令，与grep命令的不同点在于解读字符串的方法。egrep命令是使用extened regular expression语法来解读，而grep命令则是用basic regular expression语法解读，extened regular expression 比 basic regular expression的表达更规范。

### 语法
```bash
egrep [选项]... 查找模式 {文件名1,文件名2,...}
```

### 选项
```
-E, --extended-regexp     <模式> 是扩展正则表达式
  -F, --fixed-strings       <模式> 是字符串
  -G, --basic-regexp        <模式> 是基本正则表达式
  -P, --perl-regexp         <模式> 是 Perl 正则表达式
  -e, --regexp=<模式>       用指定的<模式>字符串来进行匹配操作
  -f, --file=<文件>         从给定<文件>中取得<模式>
  -i, --ignore-case         在模式和数据中忽略大小写
      --no-ignore-case      不要忽略大小写（默认）
  -w, --word-regexp         强制<模式>仅完全匹配字词
  -x, --line-regexp         强制<模式>仅完全匹配整行
  -z, --null-data           数据行以一个 0 字节结束，而非换行符

杂项:
  -s, --no-messages         不显示错误信息
  -v, --invert-match        选中不匹配的行
  -V, --version             显示版本信息并退出
      --help                显示此帮助并退出

输出控制：
  -m, --max-count=<次数>    得到给定<次数>次匹配后停止
  -b, --byte-offset         输出的同时打印字节偏移
  -n, --line-number         输出的同时打印行号
      --line-buffered       每行输出后刷新输出缓冲区
  -H, --with-filename       为输出行打印文件名
  -h, --no-filename         输出时不显示文件名前缀
      --label=<标签>        将给定<标签>作为标准输入文件名前缀
  -o, --only-matching       只显示行中非空匹配部分
  -q, --quiet, --silent     不显示所有常规输出
      --binary-files=TYPE   设定二进制文件的 TYPE（类型）；
                            TYPE 可以是 'binary'、'text' 或 'without-match'
  -a, --text                等同于 --binary-files=text
  -I                        等同于 --binary-files=without-match
  -d, --directories=ACTION  读取目录的方式；
                            ACTION 可以是`read', `recurse',或`skip'
  -D, --devices=ACTION      读取设备、先入先出队列、套接字的方式；
                            ACTION 可以是`read'或`skip'
  -r, --recursive           等同于--directories=recurse
  -R, --dereference-recursive       同上，但遍历所有符号链接
      --include=GLOB        search only files that match GLOB (a file pattern)
      --exclude=GLOB        skip files that match GLOB
      --exclude-from=FILE   skip files that match any file pattern from FILE
      --exclude-dir=GLOB    skip directories that match GLOB
  -L, --files-without-match  只打印没有匹配上的<文件>的名称
  -l, --files-with-matches  只打印有匹配的<文件>的名称
  -c, --count               只打印每个<文件>中的匹配行数目
  -T, --initial-tab         行首制表符对齐（如有必要）
  -Z, --null                在<文件>名最后打印空字符

文件控制:
  -B, --before-context=NUM  打印文本及其前面NUM 行
  -A, --after-context=NUM   打印文本及其后面NUM 行
  -C, --context=NUM         打印NUM 行输出文本
  -NUM                      等同于 --context=NUM
      --color[=WHEN],
      --colour[=WHEN]       使用标记高亮匹配字串；
                            WHEN 可以是“always”、“never”或“auto”
  -U, --binary              不要清除行尾的 CR 字符（MSDOS/Windows）
```
### 参数
##### 要查找的相关字符串
### 实例
1> 显示文件中符合条件的字符。例如：查找当前目录下所有文件中包含字符串"Linux"的文件，可以使用如下命令
```bash
egrep Linux *
```
