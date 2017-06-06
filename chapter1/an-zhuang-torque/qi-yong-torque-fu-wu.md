## 2.4 启用Torque服务

> 启用Torque服务是可选的，为了能够让Torque成为服务，您需要启用trqauthd。

启用Torque服务的方式取决于您当前使用的Linux系统。源码包中的contrib/init.d/和contrib/systemd/目录提供了启动脚本。为了能够启用服务，需要以root账号执行以下操作。

Red Hat 6

```
> cp contrib/init.d/pbs_mom /etc/init.d/pbs_mom
> chkconfig --add pbs_mom
> cp contrib/init.d/pbs_server /etc/init.d/pbs_server
> chkconfig --add pbs_server
```

SUSE 11

```
> cp contrib/init.d/suse.pbs_mom /etc/init.d/pbs_mom
> insserv -d pbs_mom
> cp contrib/init.d/suse.pbs_server /etc/init.d/pbs_server
> insserv -d pbs_server
```

Red Hat 7或SUSE 12

```
> cp contrib/systemd/pbs_mom.service /user/lib/systemd/pbs_server.service
> systemctl enable pbs_mom.service
> cp contrib/systemd/pbs_server.service /user/lib/systemd/pbs_server.service
> systemctl enable pbs_server.service
```

更多参数参见INSTALL文件

