2.5 初始化和配置Torque

本章节包含以下主题：

* [指定计算节点](/指定计算节点)
* [在计算节点上配置Torque](/在计算节点上配置Torque)
* [配置端口](/配置端口)
* [配置trqauthd](/配置trqauthd)
* [完成配置](/完成配置)

一个Torque Server（pbs\_server）包含了一个集群的所有信息。它根据TORQUE\_HOME/server\_priv/nodes文件（参见[在计算节点上配置Torque](#)）了解集群中的所有MOM节点的详细信息。它还通过集群中的MOM\(参见pbsnode\)来维护每个MOM节点的状态。

