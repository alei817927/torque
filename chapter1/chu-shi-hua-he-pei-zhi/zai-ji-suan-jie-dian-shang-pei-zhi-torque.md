## 2.7 在计算节点上配置Torque

如果您是使用Torque 自解压安装包，按照默认计算节点配置，则不需要额外步骤，请忽略本章节。

如果是手动安装，或根据需要进行高级配置，在每个节点上修改TORQUE\_HOME/mom\_priv/config文件，推荐如下配置。

TORQUE\_HOME/mom\_priv/config：

```
$logevent      1039           # bitmap of which events to log
```

可在头节点上创建该文件，并分不到其他所有节点。

