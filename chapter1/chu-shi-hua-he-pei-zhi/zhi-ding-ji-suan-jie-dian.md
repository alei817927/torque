2.6 指定计算节点

环境变量TORQUE\_HOME保存了配置文件保存的目录，如果您在安装时指定了位置，则后续无需再指定TORQUE\_HOME环境变量。

pbs\_server需要知道哪些是计算节点，在server节点文件中指定每个节点，这个文件在TORQUE\_HOME/server\_priv/nodes。在大多数情况下，每行指定节点名字即可，但是其相应属性也可以应用在节点上。

> 注意：只有root账号可以访问server\_priv目录

节点文件语法：

```
node-name[:ts] [np=] [gpus=] [properties]
```



