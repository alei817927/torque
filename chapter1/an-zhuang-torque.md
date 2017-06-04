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
> * libcgroup version: Red Hat-based systems must use libcgroup version 0.40.rc1-16.el6 or later; SUSE-based systems need to use a comparative libcgroup version.
> * libhwloc version: 1.9.1 is the minimum supported, however NVIDIA K80 requires libhwloc 1.11.0. Instructions for installing hwloc are provided as part of the Torque Resource Manager install or upgrade instructions.

* if you build Torque from source \(i.e. clone from github\), the following additional software is required:

> * gcc
> * gcc-c++
> * posix-compatible version of make
> * libtool 1.5.22 或更高版本
> * boost-devel 1.36.0 或更高版本

对对对



