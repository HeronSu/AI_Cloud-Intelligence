> [!note] 问题
> 为什么现在云计算平台不用Windows，不用UNIX，而是使用了Linux？
> - 对于目前大型的项目，运行AI大模型，云平台，需要考虑成本和生态，
> - Windows授权费高、资源开销大，UNIX绑定专有硬件、生态封闭，难以大规模扩展。
> - Linux开源免费、内核可定制、兼容x86全系硬件，且AI/容器生态（Kubernetes、CUDA）天然绑定Linux

# 1.开源与闭源
---
- 开源：将软件的源码开放，共享所有人，任何人都可以进行下载使用
	- 开源的操作系统：Linux，Android
- 闭源：不公开软件的源码，用户无法直接查看或者修改
	- 闭源的操作系统：Windows，MacOS，IOS


- 开源的几大特点
    - 开源对于用户来说是透明的，而且低风险
        - 而看得见源代码，意味着用户可以看到该系统是否存在后门，用户在使用前就可以进行判断，对于用户而言，使用时会较为放心
    - 开源同时也意味着可以定制化
        - 因为有些Linux相关的软件可能无法满足实际企业的需求，需要对其进行改造，而开放源代码就极大地便利也满足了个体或者企业定制化的需求
    - 开源也代表着低成本
        - 开源软件通常是由开源社区维护，社区多是无偿维护的，成本较低

- Linux从诞生之初就是开源的，所谓开源就是看得见源代码，可以看到源代码中有没有写入后门，用户使用放心
- 相反，对于Windows系统来说，是闭源的，并没有对外开放代码，用户并不知道Windows源代码中是否被人写入后门，如果一旦有人通过操作系统中的后门所入侵，那么损失无疑是巨大的



# 2.Linux系统的发展
---
> Linux又被称作是类UNIX


## UNIX系统

- 最早的UNIX是由美国著名实验室贝尔实验室\(Bell Laboratory\)所开发出来的
- UNIX有以下这些商业版本，UNIX最早并没有开源
    - IBM：AIX
> 	IBM的这款UNIX系统是无法安装到个人PC主机上的，它的架构较为特殊，POWER架构，IBM自己开发的精简型指令集的系统，需要使用专用硬件才可以安装，但是对于IBM公司来说，生态是较为封闭的，不对外开放，所有的硬件、软件都是自己做的，硬件软件都很稳定，对于银行，证券公司来说无疑是最佳选择。

    - HP（惠普）：UX
    - SUN：Solaris
    - OpenSSD
    - FreeBSD

---
- 指令集类型主要分为两种，复杂型指令集（CISC）和精简型指令集（RISC）
    - 复杂型指令集被IBM公司看到后，觉得不如精简型指令集，索性将其丢给了Intel，自己转头研发精简型指令集
    - 在当时Intel一家独大，为了反垄断，在美国又成立了另外一家公司AMD
    - 复杂型指令集被Intel和AMD构建了一个强大的生态和技术壁垒，系统绑定了微软的Windows，很多外设，例如鼠标，键盘，打印机等等这些外设的驱动大多也是基于复杂型指令集的X86架构来进行开发
    - 今天国内以华为为代表的研发出的ARM架构的CPU，CPU虽然做出来了，但是适配一直是一个问题，因为对于两种不同类型的指令集，意味着所有的软件要重新进行开发

- 主流的指令集架构
    - X86架构/X86\_64：复杂型指令集架构
    - POWER架构（Performance Optimized With Enhanced RISC）：IBM专有，增强 RISC 性能优化架构
    - ARM架构（Advanced RISC Machines）：精简型指令集架构




## Linux系统

- 1991年，Linus Torvalds开发出了Linux Kernel内核，并且将其开源。最后该内核和GNU项目结合，形成了Like\-UNIX，也就是Linux操作系统
> 内核官方网站：[https://www\.kernel\.org/](https://www.kernel.org/)

- 红帽是目前在Linux开源商业化做的最好的公司，红帽的Linux最早并不是RHEL，是redhat1.0，redhat2.0,...redhat9.0，然后又推出红帽企业版本的Linux，RedHat Enterprise Linux也就是RHEL，后续又陆续推出了RHEL2.0，RHEL3.0，....一直到现在最新版本的RHEL10.0
- CentOS（Community Enterprise Operation System）是由CentOS开源社区基于RHEL进行开发的，CentOS的源码与RHEL几乎是一样的，同时支持打补丁，而且免费。开源社区那些人是红帽每出一个版本，然后紧接着对应版本的CentOS也被开发出来了，所以说这个时候的CentOS较为稳定。
> CentOS归档仓库：https://vault\.centos\.org/  专门用来存放所有已停止维护（EOL）的旧版本
> CentOS官网： https://www\.centos\.org

- IBM于2019 年 7 月完成了对RedHat的收购，随之CentOS也被收购。IBM公司宣布停止对CentOS的维护，最后的版本是CentOS\-8\.5。
- IBM现在先发布CentOS Stream的版本，先发行Stream版本，这个版本并不稳定，等该版本稳定后，才会推出RHEL的正式版。

> [!important]
> 著名的数据库系统Mysql的公司SUN，同时也是开发JAVA语言的公司，最终被Oracle甲骨文所收购，至今Mysql仍然是开源的。



- 当前主流发行版本的Linux系统
    - RedHat系：RHEL，CentOS、CentOS Stream，Fedora，Rocky Linux，
> 	Rocky Linux在CentOS 8 停止维护后，由原 CentOS 创始人发起的与RHEL完全兼容的开源替代方案，可以被视为当时的CentOS
    - Debian系：Debian，Ubuntu，
    - SUSE 系：openSUSE（SUSE 的社区版），SLES \(SUSE Linux Enterprise Server\)
    - 华为：EulerOS、OpenEuler
    > 后面华为将EulerOS捐赠至开放原子开源基金会，然后开源有了OpenEuler
