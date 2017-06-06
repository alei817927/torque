## 2.8 配置端口

您可以选择各种端口用来Torque进行通信，大多数端口都可以通过不同方式配置，您可以配置的端口：

* pbs\_server监听端口
* pbs\_mom监听端口
* pbs\_server向pbs\_mom通信端口
* pbs\_mom向pbs\_server通信端口
* 客户端工具向pbs\_server通信端口
* trqauthd向pbs\_server通信端口

> 如果您在同样系统上运行了PBS专业版，请注意使用相同的环境变量和`/etc/services`。

### 2.8.1 配置pbs\_server监听端口

请按照以下步骤配置pbs\_server监听端口：

* 设置环境变量`PBS_BATCH_SERVICE_PORT`的值为您想要的端口
* 编辑`/etc/services`文件并设置`pbs port_num/tcp`
* 启动pbs\_server时附带`-p`选项

Red Hat 6或SUSE 11

编辑/etc/sysconfig/pbs\_server

```
PBS_ARGS="-p"
```

启动服务

```
service pbs_server start
```

Red Hat 7或SUSE12

编辑/etc/systemconfig/pbs\_server

```
PBS_ARGS="-p"
```

启动服务

```
systemctl start pbs_server.service
```

* 编辑`$PBS_HOME/server_name`文件并修改`server_name`为`server_name:<port_num>`
* 启动pbs\_server时附带`-H`选项

Red Hat 6或SUSE 11

编辑/etc/sysconfig/pbs\_server

```
PBS_ARGS="-H"
```

启动服务

```
service pbs_server start
```

Red Hat 7或SUSE12

编辑/etc/systemconfig/pbs\_server

```
PBS_ARGS="-H"
```

启动服务

```
systemctl start pbs_server.service
```

### 2.8.2 配置pbs\_mom监听端口

请按照以下步骤配置pbs\_mom监听端口：

* 设置环境变量`PBS_MOM_SERVICE_PORT`的值为您想要的端口
* 编辑`/etc/services`文件并设置`pbs_mom port_num/tcp`
* 启动pbs\_mom时附带`-M`选项

Red Hat 6或SUSE 11

编辑/etc/sysconfig/pbs\_mom

```
PBS_ARGS="-M"
```

启动服务

```
service pbs_mom start
```

Red Hat 7或SUSE12

编辑/etc/systemconfig/pbs\_mom

```
PBS_ARGS="-M"
```

启动服务

```
systemctl start pbs_mom.service
```

编辑pbs\_serve节点文件，添加`mom_service_port=port_num`



