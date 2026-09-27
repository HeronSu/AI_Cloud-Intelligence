# Linux语法三要素
---
- 命令（command）
- 选项（option）：为了更好的描述这个命令
- 参数（argument）

  

# 语法格式
---
- 命令、选项、参数这三个部分，每个部分中间用空格隔开
```bash
ls -l -a #等价于ls -la

ls -la

ls --help #代表一个整体，不可拆分
```

![[Pasted image 20260914202522.png]]

> [!caution] 注意事项
> - 命令、选项、参数这三个部分，每个部分中间用空格隔开，不可连接在一起
> - **顺序一定是：命令、选项、参数**，顺序错了，命令无法执行
> - 当出现多个选项时，选项与选项之间空格隔开，也可以合并为一个
> - 当出现多个参数时，参数和参数之间空格隔开

  

# 查看帮助
---
> [!tip]
> - 不要试图使用记忆去记住一切
> - 学会使用不同级别的帮助资源


- 查看帮助主要有以下几种方式
```bash
# 方式1：--help，整页输出，在纯字符界面无法翻页
(command) --help

# 方式2：man帮助手册，分页输出，用户可以自行翻页
man (command）
```

> [!tip]
> 这些man帮助手册本质上都是一堆压缩文件，在**`/usr/share/man`**目录下

![[Pasted image 20260914202916.png]]

- 下面呈现了各种命令的帮助手册文件
![[Pasted image 20260914202926.png]]

![[Pasted image 20260914202933.png]]


```bash
# 本质上就是将znew.1.gz文件进行了解压，然后看到了帮助手册
man 1 znew
```

![[Pasted image 20260914203006.png|369]]

![[Pasted image 20260914203059.png|677]]

> [!tip]
> `man`帮助手册中是针对不同人群去查看的，有的是给用户看的，有的是给开发者看的，此处不做过多解释

  

- 以`date`命令为例，查看`man`帮助手册
![[Pasted image 20260914203909.png|673]]

- `NAME`：解释该命令是用来干什么的。用来打印或者设置系统时间和日期
```bash
NAME
       date - print or set the system date and time

```


- `SYNOPSIS`：显示命令语法。
```bash
SYNOPSIS
# 两个语法中的`[]`的部分代表可选部分,...表示列表,[OPTIONS]表示可以有多个选项
       date [OPTION]... [+FORMAT]
# 这个命令中存在多个可选项，[-a|-b|-c] : 三选一
       date [-u|--utc|--universal] [MMDDhhmm[[CC]YY][.ss]]
```

```bash
# SOURCE DEST 这样的大写字母表示必选项
cp [OPTION]... [-T] SOURCE DEST
```

- `DESCRIPTION`：具体选项描述
```bash
DESCRIPTION
       Display the current time in the given FORMAT, or set the system date.

       Mandatory arguments to long options are mandatory for short options too.

       -d, --date=STRING
              display time described by STRING, not 'now'

       --debug
              annotate the parsed date, and warn about questionable usage to stderr
#-f、--file分别对应选项的短格式和长格式，长格式的存在是为了方便用户去理解选项的意思，而短格式是为了简化写法，方便用户使用
       -f, --file=DATEFILE
              like --date; once for each line of DATEFILE

```


- `EXMAPLES`：给出一些命令的例子，可以仿照这些例子去执行命令
```bash
EXAMPLES
       Convert seconds since the epoch (1970-01-01 UTC) to a date

              $ date --date='@2147483647'

       Show the time on the west coast of the US (use tzselect(1) to find TZ)

              $ TZ='America/Los_Angeles' date

       Show the local time for 9AM next Friday on the west coast of the US

              $ date --date='TZ="America/Los_Angeles" 09:00 next Fri'
```


- `man`帮助手册的基本操作
![](https://mcnpmk4g0fnx.feishu.cn/space/api/box/stream/download/asynccode/?code=ZDQyZTJmMjhmZjJlZjk3ODYyOGJiOGNmMWQ3Yjk5YzlfekllSHdQenJRNjczTGkyQmV0bG5ScnZqZzBUQ2FMbVBfVG9rZW46QW1KUmJhNXJ4b1RPN0t4eXM4MWNoc2hyblFnXzE3ODkzODg2MTk6MTc4OTM5MjIxOV9WNA&add_watermark=true&scene_type=CCM)




- `man -k` 关键字，根据关键字搜索手册。如果搜索不出来，使用`mandb`命令建立man手册的索引信息
> [!tip] 如果一个命令不会用，可以通过以下的思路查询
> 1. 先查询 `--help` ，查看命令的介绍和语法以及选项有哪些
> 2. 再查询`man`手册，有详细的介绍和选项介绍，有的手册中还记录命令的案例
> 3. 最后使用info查询，如果man手册没有则使用info手册