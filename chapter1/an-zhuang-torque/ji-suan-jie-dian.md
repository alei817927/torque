

## 2.3 计算节点

使用Adaptive Computing的Torque包创建的自解压压缩包，可以分不到计算节点安装，Torque安装包是可以定制。具体参数和特性参见其INSTALL文件。

> 如果您使用RMP安装的Torque，必须在每个节点上安装和手动修改/var/spool/torque/mom\_priv/config来配置。这个文件可以在头节点上创建然后分发到所有计算节点。
>
> ```bash
> [root]# vi /var/spool/torque/mom_priv/config
> 			
> $pbsserver     headnode    # hostname running pbs server     
> $logevent      225         # bitmap of which events to log
>  
> [root]# service pbs_mom restart
> ```

创建Torque安装包

1.configure，make，并且执行make packages

```
> make packages
Building ./torque-package-clients-linux-i686.sh ...
Building ./torque-package-mom-linux-i686.sh ...
Building ./torque-package-server-linux-i686.sh ...
Building ./torque-package-gui-linux-i686.sh ...
Building ./torque-package-devel-linux-i686.sh ...
Done.
 
The package files are self-extracting packages that can be copied and executed on your production machines. Use --help for options.
```

2.拷贝想要的内容到共享目录

```
> cp torque-package-mom-linux-i686.sh /shared/storage/
> cp torque-package-clients-linux-i686.sh /shared/storage/
```

3.在计算节点安装Torque

> 计算节点需要安装`mom-linux`，推荐您也安装其他包，这样就可以在计算节点使用客户端命令和提交任务。

下面的例子是以分布式的方式拷贝和安装`mom_linux`。

```
> for i in node01 node02 node03 node04 ; do scp torque-package-mom-linux-i686.sh ${i}:/tmp/. ; done
> for i in node01 node02 node03 node04 ; do scp torque-package-clients-linux-i686.sh ${i}:/tmp/. ; done
> for i in node01 node02 node03 node04 ; do ssh ${i} /tmp/torque-package-mom-linux-i686.sh --install ; done
> for i in node01 node02 node03 node04 ; do ssh ${i} /tmp/torque-package-clients-linux-i686.sh --install ; done
> for i in node01 node02 node03 node04 ; do ssh ${i} ldconfig ; done
```

或者您也可以使用诸如xCAT的工具来代替`dsh`。

使用类似xCAT工具

1.拷贝Torque包到节点主机上

```
> prcp torque-package-linux-i686.sh noderange:/destinationdirectory/
```

2.安装Torque

```
> psh noderange /tmp/torque-package-linux-i686.sh --install
```

您也可以把Torque Server作为计算节点使用`pbs_server`守护进程安装`pbs_mom`。

