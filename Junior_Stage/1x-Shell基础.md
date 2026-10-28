[↩️ 返回本阶段首页](../01-Linux-Cloud-Ops-Junior.md)
# Shell基础

# 1.Shell简介与功能

- 操作系统分为两个态
	- 内核态：kernel内核，pwd，ls，cd这些命令，其实内核是看不懂的。
	- 用户态：运行各种APP软件

## Shell概念

- shell是一个命令解释器，将用户执行的各种指令翻译成为内核识别的语言，换言之即将用户执行的各种命令转化为内核看得懂的语言然后进行操作
>[!tip]
>因为对于内核来说，无法识别各种命令，用户名也是通过UID来进行识别的，无法直接进行识别。所以对于shell来说使得人和机器可以相互进行交流


- Shell大致有以下几种常见类型
	- IBM AIX(Unix)：`ksh`
	- HP-UX(Unix): `csh`
	- Windows : `Powershell`
	- Linux : `Bash Shell`，使用最为常见，也被广泛使用

```bash
echo $SHELL

/bin/bash
```

- 在Linux系统中提供了以下几种Shell，在`/etc/shells`文件中记录了以下内容,但实际上也只有一个bash，这些所有的文件只是bash的一个快捷方式
```bash
/bin/sh
/bin/bash
/usr/bin/sh
/usr/bin/bash
```

> [!important]
> 注：主流的Linux发行版本是bash shell，很多命令在不同发行版本Linux上都可以执行。因为 bash shell拥有其他shell无可替代的优势
> - 语法格式宽松，**选项和参数之间一般情况下顺序无要求**，除非特殊的选项后面必须要加参数
> - 支持各种通配符（匹配文件的名）
> - 支持history历史记录
> - 支持tab键补全

- shell除了是一个命令解释器，同时也是一门脚本语言（自动化脚本），一般是以`.sh`结尾的脚本文件，可以执行相关命令的脚本


## Shell的命令扩展符

**（1）`$(command)` ：引用命令的执行结果**

- 下面举几个案例，引用`hostname`命令的执行结果
```bash
echo "This computer name is $(hostname)"

This computer name is CentOS.example.com
```

```bash
echo 'This computer name is $(hostname)'

This computer name is $(hostname)
```

>[!warning]
> 注意：造成上下两个结果不一样的原因是**单引号会剥夺命令替换的功能**。如果在文本内使用引用命令的执行结果，**一定要使用双引号`""`**


- 以日期作为文件名的一部分，这也是很常见的用法
```bash
touch $(date +%F).bak

[root@CentOS bak]# ls
2026-10-22.bak
```

- 除了上面这种引用方法，还有下面这一种，使用反引号
```bash
echo "This computer name is `hostname`"

This computer name is CentOS.example.com
```

> [!tip]
> **强烈建议只用 `$(command)`**。因为反引号极其容易和单引号混淆，而且**反引号不能嵌套**，而 `$(command)` 不仅清晰，还能无限嵌套，`$(command1 $(command2))`


**（2）`{}` ：有序重复的匹配打括号的字符串**

- 批量生成文件使用，可以创建出有规律的文件
```bash
touch test{1..10}.txt

[root@CentOS bak]# ls
test10.txt  test1.txt  test2.txt  test3.txt  test4.txt  test5.txt  test6.txt  test7.txt  test8.txt  test9.txt
```

- 也可以创建出没有规律的文件，无规律的中间使用逗号隔开
```bash
touch test{15,16,a,c}.txt

[root@CentOS bak]# ls
test15.txt  test16.txt  testa.txt  testc.txt
```


## Bash Shell的功能

- 支持history历史记录
- 支持tab键补全
- 支持各种通配符（匹配文件的名）

> [!note]
> 下面就来详细介绍Bash Shell中的通配符

|    通配符符号    | 具体含义                     |
| :---------: | ------------------------ |
|     `*`     | 匹配所有                     |
|     `?`     | 匹配任意单个字符                 |
|   `[abc]`   | 匹配该列表中的任意字符              |
|   `[0-9]`   | 匹配该列表中的任意单个数字            |
|  `[^abc]`   | 匹配除该列表以外的所有字符，仍然是去匹配单个字符 |
| `[:upper:]` | 匹配大写字母                   |
| `[:lower:]` | 匹配小写字母                   |
| `[:digit:]` | 匹配数字                     |
| `[:alnum:]` | 匹配任意字母或数字                |
- 假设某个目录下有以下内容，分别进行下面的操作
```bash
a1234.txt  a123.txt  a1.txt  a21.txt  a.txt
```

- `*`：匹配所有，执行下列命令后，当前目录便没有刚才的文件了，因为匹配所有，删除所有满足`a*.txt`这个条件的文件
```bash
rm -rf a*.txt
```


- `?`：匹配任意单个字符，删除后结果显示只删除了`a1.txt`，因为只匹配单个字符，这里面的文件`a1.txt`只有满足要求
```bash
rm -rf a?.txt

[root@CentOS bak]# ls
a1234.txt  a123.txt  a21.txt  a.txt
```

- `[abc]`：匹配该列表中的任意字符
```bash
rm -rf [abc].txt

[root@CentOS bak]# ls
a1234.txt  a123.txt  a1.txt  a21.txt
```


- `[0-9]`：匹配该列表中的任意单个数字
```bash
rm -rf a[0-9].txt

[root@CentOS bak]# ls
a1234.txt  a123.txt  a21.txt  a.txt
```




# 2.变量

## 变量的基本概念

- 概念：是可变化的量，通过变量名来找到变量的值，变化的通常是值而不是变量名；**变量定义的时候是占据内存的空间，所以这就会造成定义了变量后，如果重启机器就会发生变量失效了。

- 简化：可变化的量，变化的是值，不变的变量名，通过变量名来找到值

- 变量的命名规则
	- 变量名由数字、字母、下划线组成
	- 必须以字母或者下划线开头，**不能以数字开头**
	- 不能使用特殊字符或者空格

> [!important]
> 对于变量的命名，建议命名一些有实际意义的名字，变量毕竟是给人看的，很多时候要尽可能第一眼去识别到这个变量具体是干什么的。


## 变量定义、查看与嵌套

**1.变量定义与引用**

- 定义格式：变量名=变量值
```bash
a=100
```


- 引用变量：`$变量` 和 `${变量名}`
```bash
# 方式1
OS=redhat
[root@CentOS ~]# echo $OS
redhat

# 方式2
echo ${OS}

redhat
```

- 下面介绍几种特殊情况
```bash
echo $OS123  ----> 这里认为OS123为一个变量

# 对于这种输出指定内容且需要引用变量的，可以使用${}来对变量进行引用，
[root@CentOS ~]# echo ${OS}123 ------> 这里引用OS变量，这里就体现出${}这个用法的特殊性
redhat123
```


> [!tip]
> 同一个变量定义多次，则**后面定义的变量的值会覆盖前面变量的值**

- 对同一个变量名定义多次，查看输出结果
```bash
os=redhat
[root@CentOS ~]# os=centos
[root@CentOS ~]# echo $os
centos
```


**2.变量查看**

- 常见的查看变量的方式主要是以下这种，当然从某种角度上说，这是查看变量值，而非变量
```bash
echo $变量
echo ${变量}
```


- 变量可以查看以及取消设置，使用较少，简单介绍一下
	- `set`：直接输入命令可以查看所有定义的变量，`set`（查看环境变量和本地变量） 或者 `env`（**专门查看环境变量**）
	- `unset`：后面接上对应的变量，可以取消设置该变量



**3.变量嵌套**

- 变量的嵌套，甚至可以对变量自己进行嵌套，最为基础的嵌套如下
```bash
char1="Hello"
char2="world"
[root@CentOS ~]# echo "The sentence is $char1,$char2"
The sentence is Hello,world
```

- 命令嵌套：将命令的执行结果赋值给变量，此处`$(echo ~$user)`，外层的`$`是引用命令，内层的是引用变量
```bash
user="root"
current_dir=$(echo ~$user)
[root@CentOS ~]# echo "当前用户的家目录是：${current_dir}"
当前用户的家目录是：~root
```

- 算术嵌套：在 Shell 中做数学运算，最外层是 `$(( ))`，里面的变量可以直接写名字（不需要加 `$`），也可以加。
```bash
a=10
b=20
sum=$((a+b))
[root@CentOS ~]# echo "The final result is $sum"
The final result is 30
```

- 错误示范如下：引用变量过程中需要注意一点，对于这种使用`$()`的用法，是引用命令的执行结果的用法，要想继续引用变量，需要再加上一层括号
```bash
sum=$(a+b)
bash: a+b: command not found...
```


> [!caution]
> 注意：`$()` 可以无限嵌套，但不建议超过两层，否则代码没法看。




## 变量分类

> [!tip]
> 简单引入子进程的概念

- `pstree`：查看进程树，现在在一个终端中，连续输入`bash`，并在进程树中查看相关字段。这里只是简单介绍一下子进程，连续输入多个bash之后，可以看到出现了多个bash子进程
```bash
pstree | grep bash

	|-sshd-+-sshd---sshd---bash---bash---bash
	|      `-sshd---sshd---bash-+-grep

```

### 本地变量

- 本地（用户）变量：用户自定义的变量，**仅仅在当前shell中生效**
	- 常规的本地变量定义方式与常规变量定义方式一致：`变量名=变量值`

- 可以看到进入其他子进程时，无法读到变量值，这就是本地变量
```bash
a=100
[root@CentOS ~]# echo $a
100
[root@CentOS ~]# bash
[root@CentOS ~]# echo $a

```

---
### 环境变量

- 环境变量：系统中变量，**在当前shell和子shell中生效**
- 定义环境变量
	- 变量不存在：`export 变量名=变量值`
	- 变量已存在（将本地变量转换为环境变量）：`export 变量名`

- 再次演示上面的案例，先演示变量不存在的情况，无论在哪个终端都仍然生效
```bash
export a=100
[root@CentOS ~]# echo $a
100
[root@CentOS ~]# bash
[root@CentOS ~]# echo $a
100
[root@CentOS ~]# bash
[root@CentOS ~]# echo $a
100
```

- 变量已存在，仍然是无论哪个终端都仍然生效
```bash
 a=100
[root@CentOS ~]# export a
[root@CentOS ~]# echo $a
100
[root@CentOS ~]# bash
[root@CentOS ~]# echo $a
100
[root@CentOS ~]# bash
[root@CentOS ~]# echo $a
100
```

- 查看环境变量方式
	- `env`（**专门查看环境变量**）
	- `set`（可以查看本地变量和环境变量）

> [!important]
> 上述的本地与环境变量占据的是内存的空间，多数只在当前用户和当前终端生效，无法全局生效，而且内存的数据一旦重启就失效了。
> - 解决问题：如何使得变量永久生效？


- **全局环境变量**：在关键文件中定义全局环境变量，可以在全局生效，无论是在其他用户还是重启退出，都仍然生效
- 关键文件
```bash
ll /etc/profile
-rw-r--r--. 1 root root 2123 May 15  2020 /etc/profile
```

- 引例：添加如下字段，并测试变量是否生效
```bash
aaa=100
bbb=200
export PATH USER LOGNAME MAIL HOSTNAME HISTSIZE HISTCONTROL aaa bbb
```
修改完后，需要加载一下该文件，才可以使得变量立即生效
```bash
source /etc/profile
```

- 结果如下：可以在不同用户之间都可以读取到该变量以及变量值
```bash
echo $aaa
100
[root@CentOS ~]# echo $bbb
200
[root@CentOS ~]# su - user
[user@CentOS ~]$ echo $aaa
100
[user@CentOS ~]$ echo $bbb
200
```


- **用户环境变量**：用户环境变量优先级高于全局环境变量，即用户环境变量会覆盖全局环境变量
	- **关键文件位于每个用户家目录的**`~/.bash_profile`
> [!note]
> 为了演示读取文件的优先级，做以下演示

- 在`/etc/profile`和`~/.bash_profile`中添加`echo`字段
```bash
# /etc/profile
echo /etc/profile
```

```bash
# .bash_profile
echo ~/.bash_profile
# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi
export aaa=0
export bbb=10
# User specific environment and startup programs
```

结果显示，系统是先读取全局变量文件，然后再读取变量文件
```bash
[root@CentOS ~]# su - user
/etc/profile
/home/user/.bash_profile
```

- 立即加载文件后，可以看到当前用户读取到的变量值，已经被用户环境变量给覆盖了
```bash
echo $aaa
0
[user@CentOS ~]$ echo $bbb
10
```


---
### 系统变量

- 系统变量：特殊的环境变量
	- PATH变量存储命令的路径，可以直接执行命令的文件名而不需要执行整个绝对路径
	- 自定义一个path变量，想要操作系统上的/opt目录下的所有的可执行文件都可以当作命令来执行

> [!note]
> 常见有下面几种类型

- `HOME`：用户家目录
```bash
echo $HOME
/root
```

- `USER`：当前用户名
```bash
echo $USER
root
```

- `UID`：用户ID
```bash
echo $UID
0
```

- `PS1`：命令提示符的格式，包括当前目录、用户名等信息
	- 实际上是显示了命令提示符的几个组成部分
		- `u`：username，用户名
		- `h`：hostname，主机名
		- `W`：Work Diretory，工作目录
		- `$`：特权提示符，区分root以及普通用户
```bash
echo $PS1
[\u@\h \W]\$
```

可以通过定义本地变量来说明是否为命令提示符
```bash
[root@CentOS ~]# PS1="centos------>"
centos------>
centos------>
```


- `PWD`：当前工作目录的路径
```bash
echo $PWD
/root
```

- `PATH`：可执行文件的搜索路径，下面显示的这些路径，都是存放二进制可执行文件所在的目录，常见的使用命令都在这些目录下。**并不会到子目录中去查找**
```bash
echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
```

> [!important]
> 这个变量属于系统内比较特殊的一个变量，下面进行详细介绍

- 当用户在终端中执行命令时，系统会去系统定义的可执行文件的路径中去查找有没有对应命令的二进制可执行文件
	- 如果有，则执行
	- 如果没有，则提示`bash: lss: command not found...`

- 找不到命令有两种情况
	- 一种是命令真的不存在，例如命令错误，或者命令相关软件包没有安装
	- 一种是命令并不在预设的PATH变量路径中


## 转义

- **`\`** 去掉后面单个字符的特殊意义
该案例中，使用了弱引用，所以这里$5被认为是一个变量
```bash
echo "Your cost is $5.00"
Your cost is .00
```

加上`\`之后，可以去掉该字符的特殊意义
```bash
echo "Your cost is \$5.00"
Your cost is $5.00
```


- 引号：
    - `''` ：强引用，**去掉引号内部的所有字符的特殊意义**，原样输出
    - `" "`：弱引用，去掉引号内部的所有字符的特殊意义，**除 `$` ，反引号 ，`\` ，`!` 这些无法去掉**

```bash
# 单引号引用，强引用
echo 'Your cost is $5.00'
Your cost is $5.00
[root@CentOS ~]# echo 'Your cost is \$5.00'
Your cost is \$5.00

# 双引号引用，弱引用
[root@CentOS ~]# echo "Your cost is $5.00"
Your cost is .00
[root@CentOS ~]# echo "Your cost is \$5.00"
```

  


# 3.alias 

- `alias`别名：简化命令的执行，把长命令定义一个别名

> [!tip]
> 定义的别名可以使用tab键进行补全

- 常见使用
	- 定义别名方式：**`alias 别名='命令'`**
	- 查看别名方式：**`alias`**
	- 取消别名：**`unalias 别名`**
```bash
alias cdnetwork='cd /etc/sysconfig/network-scripts/'

alias cdsystem='cd /etc/NetworkManager/system-connections/'
```


> [!note]
> 在最小化模式下，有`ls`命令但没有`ll`命令，可以通过`alias ll='ls -l --color=auto'`来添加这个命令

- 上面的定义方式，仍是临时生效，无法永久生效，想要永久生效，写入对应用户的`~/.bashrc`或者全局`/etc/bashrc`、`/etc/bash.bashrc`


# 4.用户登录shell类型

> [!important]
> 登录shell类型和非登录shell类型区别：在于触发的变量文件不一样

- 登录shell类型：
    - `ssh`、`su -` ，**一般情况下需要输入账号密码的都算，这种情况才会获取到用户的完整的变量**，才算是完整的切换到了这个用户。

- 非登录shell类型：
    - su切换、执行脚本，**一般情况下不需要输入账号密码**，这种**非登录shell类型并没有完整的登录到这个用户**，因为有一些变量并没有获取到。


- 登录shell会触发的变量文件：
	- `/etc/bashrc`
	- `/etc/profile`
	- `~/.bash_profile`
	- `~/.bashrc`

- 读取顺序
`/etc/profile` → `/etc/profile.d/*.sh`→ `~/.bash_profile` (或 `~/.bash_login` 或 `~/.profile`) → `~/.bashrc` → `/etc/bashrc`


- 非登录shell会触发的变量文件
	- `/etc/bashrc`
	- `~/.bashrc`

> [!tip]
> 总结：如果要想定义的变量或者别名等内容或者执行脚本无论如何都要触发的话，建议是写入到`/etc/bashrc`或者`~/.bashrc`这个文件，因为这个文件无论是登录shell类型还是非登录shell类型都会触发