## 2.2 安装Torque资源管理器

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

* 如果您是源码安装（比如从
  `github`
  克隆出来），需要下面额外的环境。

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

在Torque主机上使用以下命令安装`libxml2-devel`,`openssl-devel`,`boost-devel`软件包

基于Red Hat 6和Red Hat 7的系统

```
[root]# yum install libtool openssl-devel libxml2-devel boost-devel gcc gcc-c++
```

基于SUSE 11和SUSE 12的系统

```
[root]# zypper install libopenssl-devel libtool libxml2-devel boost-devel gcc gcc-c++ make gmake
```

### 安装hwloc

> 基于Redhat系统使用`yum install hwloc`以及基于SUSE系统使用`zypper install hwloc`，安装的版本一般版本太老。

建议将`cgroups`置为`enable`，需要`hwloc1.9.1`或更高版本，NVIDIA K80需要libhwloc 1.11.0。如果要启用cgroups，检查Torque服务主机是否已经安装所需要的hwloc版本，您可以执行下面的命令检查版本号。

```
[root]# hwloc-info --version
```

下面的说明是安装版本1.9.1，如果没有安装hwloc或需要升级到所需版本，请执行以下操作：

1.在`Torque Server`主机上，每一个`Torque MOM`主机和`Torque客户端`主机，做如下操作。

* 下载
  `hwloc-1.9.1.tar.gz`
  。下载地址：
  [https://www.open-mpi.org/software/hwloc/v1.9](#)
* 执行下面的命令

基于Red Hat 6或Red Hat 7

```
[root]# yum install gcc make
[root]# tar -xzvf hwloc-1.9.1.tar.gz
[root]# cd hwloc-1.9.1
[root]# ./configure
[root]# make
[root]# make install
```

基于SUSE 11或SUSE 12

```
[root]# zypper install gcc make
[root]# tar -xzvf hwloc-1.9.1.tar.gz
[root]# cd hwloc-1.9.1
[root]# ./configure
[root]# make
[root]# make install
```

2.基于Red Hat 6或Red Hat 7的系统，只在Torque Server主机下执行以下命令

```
[root]# echo /usr/local/lib 
>
/etc/ld.so.conf.d/hwloc.conf
[root]# ldconfig
```

3.基于SUSE11的系统，每一个`Torque MOM`主机和`Torque客户端`主机执行以下命令

```
[root]# echo /usr/local/lib >/etc/ld.so.conf.d/hwloc.conf
[root]# ldconfig
```

4.基于SUSE12的系统，只在`Torque Server`主机上执行以下命令

```
[root]# echo /usr/local/lib >/etc/ld.so.conf.d/hwloc.conf
[root]# ldconfig
```

### 安装Torque Server

> 在安装Torque Server之前，需要完成依赖、软件包和客户端，完成端口的安装。另外如果有防火墙，要把需要的端口打开。

在Torque Server主机上，按照以下步骤操作

1.下载6.1.1版本，可以从Adaptive Computing官网下载构建，也可以通过命令行下载。

* 从github克隆源码（如果没有安装git，请自行安装）。

```
[root]# git clone https://github.com/adaptivecomputing/torque.git -b 6.1.1 6.1.1 
[root]# cd 6.1.1
[root]# ./autogen.sh
```

* 下载源码版压缩包

基于Red Hat 6或Red Hat 7

```
[root]# yum install wget
[root]# wget http://www.adaptivecomputing.com/download/torque/torque-6.1.1.tar.gz -O torque-6.1.1.tar.gz
[root]# tar -xzvf torque-6.1.1.tar.gz
[root]# cd torque-6.1.1/
```

基于SUSE 11或SUSE 12

```
[root]# zypper install wget
[root]# wget http://www.adaptivecomputing.com/download/torque/torque-6.1.1.tar.gz -O torque-6.1.1.tar.gz
[root]# tar -xzvf torque-6.1.1.tar.gz
[root]# cd torque-6.1.1/
```

2.根据您的系统配置，您需要添加./configure选项，至少需要添加以下两项。

* ‑‑enable‑cgroups
* ‑‑with‑hwloc‑path=/usr/local

> 当前是假设您已经在使用cgroups，当cgroups已经支持，cpusets就会由cgroup cpuset 子系统来处理。如果没有使用cgroups，请使用`‑‑enable‑cpusets`代替。

3.按照顺序依次执行下面的命令

```
[root]# ./configure --enable-cgroups --with-hwloc-path=/usr/local # add any other specified options
[root]# make
[root]# make install
```

4.执行配置文件将`/usr/local/bin`和`/usr/local/sbin`添加到您的path中

```
[root]# . /etc/profile.d/torque.sh
```

5.执行`torque.setup`脚本初始化`serverdb`

```
[root]# ./torque.setup root
```

6.添加节点到`/var/spool/torque/server_priv/nodes`文件中

7.将pbs\_server配置系统自启动，并且启动守护进程。

基于Red Hat 6的系统

```
[root]# chkconfig --add pbs_server
[root]# service pbs_server restart
```

基于Red Hat 7的系统

```
[root]# qterm
[root]# systemctl enable pbs_server.service
[root]# systemctl start pbs_server.service
```

基于SUSE 11的系统

```
[root]# chkconfig --add pbs_server
[root]# service pbs_server restart
```

基于SUSE 12的系统

```
[root]# qterm
[root]# systemctl enable pbs_server.service
[root]# systemctl start pbs_server.service
```

### 安装Torque MOMs

您将在每一个计算节点上安装Torque MOM，请按照以下步骤操作：

1.在Torque Server主机上操作

a.创建自解压安装包

```
[root]# make packages
Building ./torque-package-clients-linux-x86_64.sh ...
Building ./torque-package-mom-linux-x86_64.sh ...
Building ./torque-package-server-linux-x86_64.sh ...
Building ./torque-package-gui-linux-x86_64.sh ...
Building ./torque-package-devel-linux-x86_64.sh ...
Done.


The package files are self-extracting packages that can be copied and executed on your production machines.  Use --help for options.
```

b.拷贝自解压MOM安装包到每个MOM主机，Adaptive Computing建议您直接执行远程脚本，比如使用SSH远程安装，您可以使用共享key的方式操作，可以避免输入密码。

```
[root]# scp torque-package-mom-linux-x86_64.sh <mom-node>:
```

c.拷贝pbs\_mom启动脚本到Torque MOM主机

Red Hat 6

```
[root]# scp contrib/init.d/pbs_mom <mom-node>:/etc/init.d
```

Red Hat 7

```
[root]# scp contrib/systemd/pbs_mom.service <mom-node>:/usr/lib/systemd/system/
```

SUSE 11

```
[root]# scp contrib/init.d/suse.pbs_mom <mom-node>:/etc/init.d/pbs_mom
```

SUSE 12

```
[root]# scp contrib/systemd/pbs_mom.service <mom-node>:/usr/lib/systemd/system/
```

2.针对Red Hat 6或SUSE 11，确认每个Torque MOM主机安装了cgroups，如果没有挂载，请挂载。

a.运行[lssubsys -am](#)

b.如果无此命令或者看不到下面类似的内容，说明cgroups没有挂载，请继续后续操作

```
ns
perf_event
net_prio
cpuset /cgroup/cpuset
cpu /cgroup/cpu
cpuacct /cgroup/cpuacct
memory /cgroup/memory
devices /cgroup/devices
freezer /cgroup/freezer
net_cls /cgroup
et_cls
blkio /cgroup/blkio
```

c.对于Red Hat 6，安装cgroup库，挂载cgroups

```
[root]# yum install libcgroup
[root]# service cgconfig start
[root]# chkconfig cgconfig on
```

d.针对SUSE 11，请做如下操作

* 安装cgroup库

```
[root]# zypper install libcgroup1
```

* 在配置文件
  `/etc/cgconfig.conf`
  添加以下内容

```
mount {
        devices = /mnt/cgroups/devices;
        cpuset = /mnt/cgroups/cpuset;
        cpu = /mnt/cgroups/cpu;
        cpuacct = /mnt/cgroups/cpuacct;
        memory = /mnt/cgroups/memory;
}
```

* 挂载cgroups

```
[root]# service cgconfig start
[root]# chkconfig cgconfig on
```

e.再次运行[lssubsys -am](#)确认cgroups已挂载

3.针对每个Torque MOM做如下操作

a.在Red Hat 7或SUSE 12下安装cgroup-tools

Red Had 7

```
[root]# yum install libcgroup-tools
```

SUSE 12

```
[root]# zypper install libcgroup-tools
```

b.安装自解压MOM包

```
[root]# ./torque-package-mom-linux-x86_64.sh --install
```

c.配置pbs\_mom为系统自启动，作为守护进程启动。

Red Hat 6

```
[root]# chkconfig --add pbs_mom
[root]# service pbs_mom start
```

Red Hat 7

```
[root]# systemctl enable pbs_mom.service
[root]# systemctl start pbs_mom.service
```

SUSE 11

```
[root]# chkconfig --add pbs_mom
[root]# service pbs_mom start
```

SUSE 12

```
[root]# systemctl enable pbs_mom.service
[root]# systemctl start pbs_mom.service
```

### 安装Torque客户端

如果您希望在其他主机上安装Torque客户端（比如计算节点和独立登录节点），请执行以下操作

1.在Torque Server主机上做以下操作

a.将自解压客户端安装包拷贝到Torque Client主机上

```
[root]# scp torque-package-clients-linux-x86_64.sh <torque-client-host>:
```

b.拷贝trqauthd启动脚本到每个客户端主机

Red Hat 6

```
[root]# scp contrib/init.d/trqauthd <torque-client-host>:/etc/init.d
```

Red Hat 7

```
[root]# scp contrib/systemd/trqauthd.service <torque-client-host>:/usr/lib/systemd/system/
```

SUSE 11

```
[root]# scp contrib/init.d/suse.trqauthd <torque-client-host>:/etc/init.d/trqauthd
```

SUSE 12

```
[root]# scp contrib/systemd/trqauthd.service <torque-client-host>:/usr/lib/systemd/system/
```

c.在Torque Client主机上安装自解压客户端包

```
[root]# ./torque-package-clients-linux-x86_64.sh --install
```

### 配置数据管理

当批处理作业完成时，stdout和stderr文件会生成到Torque Server master主机上的spool目录，而不会生成在提交主机上。您可以修改环境配置，将stdout和stederr文件写回提交主机上。

