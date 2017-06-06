## 2.5 初始化和配置Torque

本章节包含以下主题：

* [指定计算节点](/指定计算节点)
* [在计算节点上配置Torque](/在计算节点上配置Torque)
* [配置端口](/配置端口)
* [配置trqauthd](/配置trqauthd)
* [完成配置](/完成配置)

一个Torque Server（pbs\_server）包含了一个集群的所有信息。它根据`TORQUE_HOME/server_priv/nodes`文件（参见[在计算节点上配置Torque](#)）了解集群中的所有MOM节点的详细信息。它还通过集群中的MOM\(参见pbsnode\)来维护每个MOM节点的状态。所有作业都由qsub提交到Torque Server，Torque Server维护了数据库记录所有作业和它们的状态。

诸如`Moab Workload Manager`的调度器从`pbs_server`接收作业、队列、节点信息，同时也将所有作业提交到`pbs_server`。

服务器配置信息保存在`serverdb`文件中，文件位于`TORQUE_HOME/server_priv`目录下面，包含了所有Torque操作的相关参数，以及配置中所有队列。

您有两种方式初始化`serverdb`，不过建议您使用`./torque.setup`操作：

* 在构建目录使用root账号执行`./torque.setup`脚本
* 执行`pbs_server -t create`

初始化serverdb后重启pbs\_server

* Red Hat 6或SUSE 11

```
> qterm 
> service pbs_server start
```

* Red Hat 7或SUSE 12

```
> qterm 
> systemctl start pbs_server.service
```

### 2.5.1 ./torque.setup脚本

`./torque.setup`脚本使用`pbs_server -t create`创建并初始化`serverdb`，然后再添加一个用户作为Torque和其他常用属性的管理器和操作员。具体语法如下：

./torque.setup _username_

```
> ./torque.setup ken
> qmgr -c 'p s'

#
# Create queues and set their attributes.
#
#
# Create and define queue batch
#
create queue batch  set queue batch queue_type = Execution
set queue batch resources_default.nodes = 1
set queue batch resources_default.walltime = 01:00:00
set queue batch enabled = True
set queue batch started = True
#
# Set server attributes.
#
set server scheduling = True
set server acl_hosts = kmn
set server managers = ken@kmn
set server operators = ken@kmn
set server default_queue = batch
set server log_events = 511
set server mail_from = adm
set server node_check_rate = 150
set server tcp_timeout = 6
set server mom_job_sync = True
set server keep_completed = 300
```

一个名为`batch`的队列和服务器需要的其他属性创建完成。

### 2.5.2 pbs\_server -t create命令

`-t` 参数指示pbs\_server生成并初始化serverdb，并用最少配置来运行pbs\_server。

```
> pbs_server -t create
```

使用`qmgr`来查看配置内容和验证配置是否正确：

```
> qmgr -c 'p s'
 
#
# Set server attributes.
#
set server acl_hosts = kmn
set server log_events = 511
set server mail_from = adm
set server node_check_rate = 150
set server tcp_timeout = 6
```

### 2.5.3 为pbs\_server和pbs\_mom设置环境

`pbs_environment`文件\(默认位置`TORQUE_HOME/pbs_environment`\)将在启动时由`pbs_mom`和`pbs_server`执行。如果需要设置`pbs_server`和`pbs_mom`环境变量参数，可以替换此文件内容。另外可以在编译之前使用`--with-environ=PATH`参数来指定配置文件，运行`pbs_server --about`命令可查看是否指定配置文件。

> 请不要将`pbs_environment`文件和作业的环境变量`PBS_ENVIRONMENT`混淆。



