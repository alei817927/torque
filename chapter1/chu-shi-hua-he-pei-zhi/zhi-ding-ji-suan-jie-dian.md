## 2.6 指定计算节点

环境变量`TORQUE_HOME`保存了配置文件保存的目录，如果您在安装时指定了位置，则后续无需再指定`TORQUE_HOME`环境变量。

pbs\_server需要知道哪些是计算节点，在server节点文件中指定每个节点，这个文件在`TORQUE_HOME/server_priv/nodes`。在大多数情况下，每行指定节点名字即可，但是其相应属性也可以应用在节点上。

> 注意：只有root账号可以访问server\_priv目录

节点文件语法：

```
node-name[:ts] [np=] [gpus=] [properties]
```

* `node-name`必须和每个节点在host中的名字一致。
* `[:ts]`选项标记了节点是timeshared。timeshared节点在状态列表中会被列出，但服务器给其分配作业。
* `[np=]`选项指定给定节点的虚拟处理器数量。这个值可小于、等于或大于任何给定节点上的物理处理器数量。
* `[gpus=]`选项指定给定节点的gpu数量。该值可以小于、等于或大于任何给定节点上的物理gpu数量。
* 如果auto\_node\_np设置为TRUE，Torque Server可以自动检测到节点处理器计数。可使用下面命令设置：

```
qmgr -c 'set server auto_node_np = True'
```

此处设置auto\_node\_np为TRUE将会覆盖`TORQUE_HOME/server_priv/nodes`的np值。

* \[properties\]选项允许您指定任意字符串来标识节点。properties值由字母数字组合，必须以字母开始。
* 节点文件中允许注释行，第一个非空格字符是\#表示当前行为注释。

下面为节点文件内容示例，TORQUE\_HOME/server\_priv/nodes:

```
# Nodes 001 and 003-005 are cluster nodes
#
node001 np=2 cluster01 rackNumber22
#
# node002 will be replaced soon
node002:ts waitingToBeReplaced
# node002 will be replaced soon
#
node003 np=4 cluster01 rackNumber24
node004 cluster01 rackNumber25
node005 np=2 cluster01 rackNumber26 RAM16GB
node006
node007 np=2
node008:ts np=4
...
```



