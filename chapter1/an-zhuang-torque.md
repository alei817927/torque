## Torque体系结构

一个Torque集群是有一个头结点和多个计算节点组成，头结点运行了`pbs_server`守护进程，并且计算节点运行了`pbs_mom`守护进程。任何主机都可以安装提交和管理作业的客户端命令（包含了没有运行`pbs_server`和`pbs_mom`的主机）。

头节点还运行一个调度器守护进程，调度器与`pbs_server`进行交互，根据本地策略决定资源的使用，并将节点分配给作业。Torque源码版本提供了一个简单的先进先出`FIFO`调度器和代码来构造更高级的调度器。大多数Torque用户选择使用高级调度器，如`Maui`或`Moab`。

用户使用`qsub`命令向`pbs_server`提交作业，当`pbs_server`收到一个新的作业时，它会通知调度器，当调度程序为作业找到节点时，它会将此作业执行指令和节点列表一起发送到`pbs_server`。然后`pbs_server`将新作业发送到节点列表中的第一个节点，并指示它启动作业。这个节点被指定为执行主机。

## 安装Torque资源管理器

> 如果您使用Moab Workload Manager集成了Torque Resource Manager 6.1.1，您必须使用版本号大于等于8.0的`Moab`，尽管如此，有些特性无法生效，具体参见“Compatibility Requirements”。

本章节介绍如何安装和开始Torque资源管理器（Torque）

> _针对Cray系统，Adaptive Computing公司建议您在商用硬件上安装_`Moab`_和_`Torque Servers`_（head nodes），不包括Cray compute/service/login节点。但是您必须在登录和“mom”服务节点上安装Torque pbsmom守护进程和Torque客户端命令。因为_`pbs_mom`_必须在Cray系统中的Cray服务节点上运行，以便能够访问_`Cray ALPS`_子系统。_

### 系统和软件需求

支持的操作系统

* CentOS 6.x, 7.x 
* RHEL 6.x, 7.x 
* Scientific Linux 6.x, 7.x 
* SUSE Linux Enterprise Server 11, 12, 12-SP1

软件需求

* libxml2-devel package \(包名称可能不同\)
* openssl-devel package \(包名称可能不同\)
* Tcl/Tk .如果您准备构建Torque GUI部分，或者使用基于Tcl的调度器，请使用Tcl/Tk版本8或更高版本。
* cpusets 和 cgroups

> cgroups are supported and cpusets are handled by the cgroup cpuset subsystem
>
> 建议您使用‑‑enable‑cgroups instead of ‑‑enable‑cpuset. ‑‑enable‑cpuset
>
> * boost version: 1.41 或更高版本
>
> * libcgroup: 基于Red Hat系统必须使用`libcgroup 0.40.rc1-16.el6`或更高版本，基于SUSE系统需要使用`comparative libcgroup`版本。
>
> * libhwloc: 最低版本是1.9.1，但是NVIDIA K80需要`libhwloc 1.11.0`。

* 如果您是源码安装（比如从`github`克隆出来），需要下面额外的环境。

> * gcc
> * gcc-c++
> * posix-compatible version of make
> * libtool 1.5.22 或更高版本
> * boost-devel 1.36.0 或更高版本
>
> 基于Red Hat 6系统打包为1.4 1.0，基于Red Hat 7的系统打包为1.53.0。如果需要，使用`--with-boost-path=DIR`选项来更改打包的boost版本。参见2.12自定义安装以获得更多信息。

## 需要开放的端口

Torque需要开放某些端口用来必要的通信。如果您的主机有防火墙，需要配置防火墙并能开放这些端口。

| 位置 | 端口 | 功能 |
| :--- | :--- | :--- |
| Torque Server Host | 15001 | Torque客户端、MOM与Torque Server通信。 |
| Torque MOM Host \(Compute Nodes\) | 15002 | Torque Server和Torque MOMs通信。 |
| Torque MOM Host \(Compute Nodes\) | 15003 | Torque MOM和其他Torque MOMs通信 |

## 安装依赖、软件包、客户端

### 安装软件包

在Torque主机上使用以下命令安装`libxml2-devel`, `openssl-devel`, `boost-devel`软件包

基于Red Hat 6和Red Hat 7的系统

```bash
[root]# yum install libtool openssl-devel libxml2-devel boost-devel gcc gcc-c++
```

基于SUSE 11和SUSE 12的系统

```bash
[root]# zypper install libopenssl-devel libtool libxml2-devel boost-devel gcc gcc-c++ make gmake
```

### 安装hwloc

> 基于Redhat系统使用`yum install hwloc`以及基于SUSE系统使用`zypper install hwloc`，安装的版本一般版本太老。

建议将`cgroups`置为`enable`，需要`hwloc1.9.1`或更高版本，NVIDIA K80需要libhwloc 1.11.0。如果要启用cgroups，检查Torque服务主机是否已经安装所需要的hwloc版本，您可以执行下面的命令检查版本号。

```bash
[root]# hwloc-info --version
```

下面的说明是安装版本1.9.1，如果没有安装hwloc或需要升级到所需版本，请执行以下操作：

1.在`Torque Server`主机上，每一个`Torque MOM`主机和`Torque客户端`主机，做如下操作。

* 下载`hwloc-1.9.1.tar.gz`。下载地址：[https://www.open-mpi.org/software/hwloc/v1.9](https://www.open-mpi.org/software/hwloc/v1.9)
* 执行下面的命令

基于Red Hat 6或Red Hat 7

```bash
[root]# yum install gcc make
[root]# tar -xzvf hwloc-1.9.1.tar.gz
[root]# cd hwloc-1.9.1
[root]# ./configure
[root]# make
[root]# make install
```

基于SUSE11或SUSE12

```bash
[root]# zypper install gcc make
[root]# tar -xzvf hwloc-1.9.1.tar.gz
[root]# cd hwloc-1.9.1
[root]# ./configure
[root]# make
[root]# make install
```

2.基于Red Hat 6或Red Hat 7的系统，只在Torque Server主机下执行以下命令

```bash
[root]# echo /usr/local/lib >/etc/ld.so.conf.d/hwloc.conf
[root]# ldconfig
```

3. 基于SUSE11的系统，每一个`Torque MOM`主机和`Torque客户端`主机执行以下命令

```bash
[root]# echo /usr/local/lib >/etc/ld.so.conf.d/hwloc.conf
[root]# ldconfig
```

4. 基于SUSE12的系统，只在`Torque Server`主机上执行以下命令

```bash
[root]# echo /usr/local/lib >/etc/ld.so.conf.d/hwloc.conf
[root]# ldconfig
```

### 安装Torque Server



