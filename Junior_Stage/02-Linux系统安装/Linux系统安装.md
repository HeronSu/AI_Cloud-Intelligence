> [!important]
> 此处演示CentOS 8.4版本的Linux安装过程 ，其他版本的操作系统安装流程也差不多

- 现在安装Linux系统主要会被安装在两个地方
    - 安装在物理服务器上
    - 安装在个人的VMware Workstation的虚拟机上（个人学习测试使用）

>[!caution]
不要将VMware Workstaion用于实际生产环境，这个是作为个人用途


# 系统安装前操作
---
## 创建虚拟机 
---
> 创建新的虚拟机有以下两种⽅式 

- 在主页创建新虚拟机 
![Junior_Stage/02-Linux系统安装/assets/image\.png|663](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%202.png)


-  在界⾯左上角文件处-------新建虚拟机
![image\.png|627](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2010.png)


- 此处**选择自定义（高级**），不要选择典型（推荐）
> 选择推荐，其实省略了很多中间过程，实际工作中是给⼀个服务器去安装⼀个操作系统，服务器无法自动化为你去安装这个操作系统，所以中间的所有过程都需要我们了解。  

![image\.png|474](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2041.png)


-  硬件兼容性根据当前 VMware Workstation 的版本来进行选择
![image\.png|481](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2031.png)





- 选择安装来源：选择稍后安装操作系统 
![image\.png|488](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2033.png)

> [!caution]
> **不要选择安装程序光盘镜像文件（iso），始终面向服务器，中间的⼀些配置都需要我们自己去设置 **



- 根据实际需要来选择操作系统类型
    - 如果要安装 Windows 虚拟机，就选择 Microsoft Windows(W)，然后再选择你要安装Windows 系统的版本，如 Windows 11 x64 
    - 如果要安装 Linux 虚拟机，就选择 Linux(L)，然后选择对应 Linux 的系统版本，例如例如 Red Hat Enterprise Linux 9，CentOS 8 64 位 
![image\.png|471](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2023.png)




- 自定义虚拟机名称，路径注意不要在中文路径下，创建⼀个文件夹，虚拟机所有文件都在此文件夹下。**建议不要将Linux的ISO文件放在该文件夹下**，该文件夹下只放与虚拟机有关的文件，镜像属于外部文件。

![image\.png|465](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2026.png)

> [!tip]
> 虚拟机本质上就是⼀堆文件，Linux一切皆文件，所以放在文件夹下，既方便查找和移动虚拟机至其他 PC 上进行使用，又方便后期删除。



- 处理器配置相关解释：根据自己PC的实际情况进行选择，想要系统运行的更流畅，可以适当给多⼀点，后期也可以进行调整
    - 处理器数量：就是物理 CPU，看得见摸得着的那个硬件（主板） 
    - 内核数量：⼀颗 CPU 的核⼼数 
    - 对于操作系统来说，CPU 的数量不是看处理器数量，也不是看内核数量，⽽是看 “处理器数量 \* 内核数量”

![image\.png|498](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%209.png)



- 虚拟机内存根据需要进行选择，同样想要系统运行的更流畅，可以适当给多一点，后期也可以进行调整。

![image\.png|494](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2011.png)



- 网络类型根据需要进行选择 
    - 桥接网络：直接桥接到宿主机的物理网卡上，实际上访问 Internet 就是通过宿主机的物理网卡来访问 Internet 的 
    - NAT 网络：虚拟机是可以访问 Internet 的，将虚拟机的内部 ip 地址转化为宿主机的物理网卡的IP地址出去，只要宿主机可以上网，NAT模式下的虚拟机也⼀定可以上网。 
    - 仅主机模式网络：如果都是仅主机模式下的虚拟机，虚拟机之间是都可以通信的，但无法访问，无法访问外部网络。

![image\.png|451](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2030.png)




- 根据实际需要进行选择即可，此处保持默认即可

![image\.png|456](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2012.png)



- 磁盘类型根据需要进行选择，此处保持默认即可 
    - 如果是真实服务器，那么根据实际插在服务器的磁盘类型进行选择，例如 NVMe⼀般是固态硬盘，性能较好，SATA ⼀般是机械硬盘，存储容量大，根据实际需要进行选择

![image\.png|442](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2043.png)



- 此处由于是创建新虚拟机，选择创建新虚拟磁盘 
> 操作系统实质上就是安装到某⼀个盘，虚拟机本质就是⼀堆文件，而对于磁盘来说也是一个文件，在vmware虚拟化中是⼀个vmdk文件，文件类型属于VMware的虚拟磁盘 
    - 创建新虚拟磁盘：创建新虚拟机时使用
    - 使用现有磁盘：已有vmdk的磁盘文件，可以直接使用该磁盘作为操作系统的存储 
    - 使用物理磁盘：针对服务器场景

![image\.png|466](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2017.png)




- 磁盘大小选择可以任意，选择将虚拟磁盘存储为单个⽂件，可以适当调整大小
> 对于虚拟机来说，磁盘是精简配置，⽤多少占用多少空间，200GB不会占用原主机200G的空间

	- 如果说追求性能，选择将虚拟磁盘存储为单个文件
	- 如果说需要创建虚拟机之后对其进行迁移，选择将虚拟磁盘拆分成多个文件

![image\.png|470](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2042.png)



- 这就是刚才创建的磁盘文件
![image\.png|480](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2036.png)



- 确认配置，创建虚拟机完成
![image\.png|483](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2037.png)






## 挂载ISO并开启虚拟机

- 编辑虚拟机设置 
![image\.png|649](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2032.png)



- 确保启动时连接，选择对应版本的 ISO 镜像 
![image\.png|656](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2035.png)


- 开启虚拟机，进⼊安装 Linux 系统过程
![image\.png|662](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2027.png)





# Linux操作系统安装
> 此处演示CentOS 8.4的版本安装，同为RHEL系列的Linux系统大体安装过程差不多
---
## 进入安装界面
![image\.png](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2015.png)

- 选项解释（从上至下依次解释），使用``Esc``回退到上一个界面
    - 直接安装操作系统（在实际生产环境中并不怎么推荐）
    - 先校验光盘的iso镜像是否完整，然后再安装操作系统
    - 紧急救援模式

![image\.png](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2020.png)


- 进入救援模式，下面对`Troubleshooting`中的选项做具体解释
    - 在基本图形模式（basic graphics mode）下安装Linux
> 正常情况下，CentOS 安装程序会尝试加载完整的高级显卡驱动来显示安装界面，但如果你的显卡比较或者配置较高，虚拟机在标准安装模式可能会导致黑屏、卡死，或者无法显示图形安装界面。
> 
> “基本图形模式”会禁用高级显卡驱动，改用最基础的 VGA 驱动来渲染界面，保证安装向导能正常显示出来。

    - text mode字符页面安装：
    > 安装操作系统分为两类，图形化和字符界面
    > 
    > 图形化：所有的配置可以使用鼠标点击
    > 
    > 字符：所有的配置都没有图形化，就是一个纯字符界面，只能用键盘操作
    > 
    > 

    - 救援模式：系统崩溃，重置管理密码，修复Linux系统的故障（类似于windows的PE）
    - 校验内存
    - 从当前的硬盘启动
    - 返回主菜单
- 如果系统盘超过了2TB，需要单独划分/boot/EFI区域





## 选择语言

- 此处语言**默认选择英文**，可以选择中文，但不建议
> [!important]
> 对于所有的Linux系统而言，**英语的兼容性无疑是最好的**，不管是在图形化界面，还是纯字符界面都不会出现问题，相反，中文会在纯字符界面时产生问题，报错时可能会产生乱码

![image\.png](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2034.png)





## 安装配置

### I. LOCALLIZATION（本地化）

![image\.png|658](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%204.png)

- Keyboard（键盘布局）：选择英文，保持默认即可
- Language Support（语言支持）
    - 可以添加中文的语言支持，这里英文是默认选项
> 刚才选择的语言是英文代表默认是英文，这里添加中文，意味着系统也可以识别中文

![image\.png|633](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2024.png)

![image\.png|293](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2021.png)



- Time\&Date（时间和日期）
    - Region（地区）：Asia
    - City（城市）：Shanghai
    - 修改时间和日期
    - Network Time（网络时间）：这个开关无法打开，是因为当前需要连接到互联网或局域网中的时间服务器来同步时钟，这里并没有进行配置，所以无法打开
![image\.png](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%206.png)

> [!note]
> - 时间对于一台计算机来说是很重要的，误差需要到毫秒级，甚至是微秒级，纳秒级。
> - 基于这个需求，需要搭建一个时间服务器，用于给其他服务器来进行同步时间，时间服务器是可以访问Internet的，但对于其他服务器来说，一般是不能访问Internet的




### II. SOFTWARE（软件）

- Installation Source（安装源）：保持默认即可
- Software Selection（软件选择）
    - Server with GUI（带图形化的界面）：对初学者比较友好
    - Minimal Install（最小化安装）：安装包很少，纯字符界面，没有图形化，大部分软件包也没有，对于初学者不友好，但是在生产环境中更多使用最小化安装

> [!important]
> **即便有图形界面，也不会去使用**，通常是远程连接到Linux系统中进行相关操作，操作的界面还是命令行

![image\.png](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image.png)



### III. SYSTEM（系统）

![image\.png|213](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2025.png)

- Installation Destination（安装目的地）
> 本质上是对磁盘进行分区

> [!tip]
> - 在Linux中，一切皆文件，包括设备，例如/dev/sda，/dev/vda这些存储设备
> - 在Windows 服务器版本中，如果对系统重装，无法将用户数据、网站配置导出，但是Linux可以做到

![image\.png|665](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2044.png)

- Storage Configuration（存储配置）
    - Automatic（自动）
    - Custom（手动）：此处选择手动分区，点击两次Done

> [!caution]
> 实际工作中，不要选择自动分区，因为自动分区会将磁盘空间分配完，后续想要再去调整，没有空间了



- 分区类型选择Standard Partition 标准分区
![image\.png|637](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2029.png)

- 分区划分
> 此处分区划分大小可以根据实际情况进行调整
	
	- `/boot`：包含内核文件，引导配置文件。划分500M即可，不必比这个更大
	- `/`：根分区，包含内核文件，引导配置文件。划分30G
    > [!tip]
    > 实际工作中，根分区大小根据部署什么应用，应用大概占用多少磁盘空间来进行分配

	- `swap`：交换分区，充当的是内存的作用，把硬盘的空间当作内存来使用，运行更多的软件，但有概率会影响运行速度性能。划分4G
	> swap分区跟内存相关，实际工作中，分配16G\~64G,内存越大建议swap越大，未来可以扩展swap大小
	- `/home`：家目录，各个用户的家目录，所有普通用户的文件默认都存储在这个目录下面。划分5G
	- `/data`：划分5G

![image\.png|380](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2018.png)

- **`biosboot`**：如果系统盘超过2TB（也就是整个硬盘超过2TB），需要对这个分区分一个区，或者`/boot/efi`分区，此时分区类型为EFI System Partition

- 分区结果如下

![image\.png|666](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%201.png)



- 选择Accept Changes（接收改变）
![image\.png](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%208.png)




- Network&Hostname（网络和主机名）
	- 修改主机名，安装完系统后，也可以在系统内进行修改，点击应用后完成
    - 开启网络，此时网络信息是通过DHCP自动获取的

![image\.png|669](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2028.png)



- 进入配置，勾选网卡自动激活，即开机后网卡会自动启动，不会被禁用
![image\.png|662](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2040.png)




- 对IPV4网络信息进行手动配置，配置IP地址，子网掩码，网关以及DNS服务器，然后点击Save保存
![image\.png|647](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2022.png)



- 手动配置后，对网络关闭再开启，使刚才的配置生效，配置完成结果如下
![image\.png|644](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2038.png)


- kdnump内核崩溃转储机制：**关掉**
> 注：**平常用不上，也不想看到它，会占用内存**。如果看到kdnump，那么整个操作的内核都挂了，整个转储会收集系统的信息然后保存为一个文件/var/crash



### IV. USER SETTINGS（用户设置）

![image\.png|577](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2045.png)


- Root Password（Root超级管理员的密码）：此处用于实验，密码可以设置的简单一点
![image\.png|634](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2019.png)




- User Creation（创建用户）
    - 此处创建用户也可以不创建，但是安装完成之后，系统还是会要求你创建一个用户
    - 不要勾选“Make this user administrator”，**一个系统内只能有一个超级管理员**
![image\.png|652](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%207.png)



## 进入安装过程
---
- 上述所有配置过程完成之后，进入安装过程
![image\.png|653](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%203.png)




- 安装完成后，重启系统
![image\.png](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2014.png)



## 进入系统
---
- 选项解释，常规模式下默认进入第一个
	- 系统
	- 进入救援模式
![image\.png](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%205.png)



- License Information（证书信息）
![image\.png|455](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2039.png)



- 勾选同意
![image\.png|654](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2016.png)



- 进入Not Listed，用root用户登录，依次输入用户名和密码

![image\.png|663](AI云智算/Junior%20Stage/02-Linux系统安装/assets/image%2013.png)


> [!warning]
> 图形界面其实安全性很低，主要体现在以下几个方面
> - 在图形化界面输密码是可以看到密码位数的，相反字符界面密码不是明文显示的，如果别人看到了密码位数，对于破密码来说范围就缩小了，破密会更加容易
> - 图形化界面**所有用户**可以直接对Linux操作系统进行关机操作，这对于实际生产操作来说是十分危险的，一般服务器都是常年不关机的，而且一般用户是没有权限对服务器进行关机操作的




























































