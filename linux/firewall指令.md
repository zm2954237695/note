firewall指令

```bash
打开端口：firewall -cmd --permanent --add-port=端口号/协议
关闭端口：firewall -cmd --permanent --remove-port=端口号/协议
重新载入，才能生效: firewall -cmd --reload
查询端口是否开放: firewall -cmd --query-port=端口/协议
```

通过使用以下命令，

```bash
ls -l /etc/init.d/
```

netconsole,network,vmare-tools还可以用service命令查询

```bash
service start|stop|restart|reload|status
```

使用setup可以查看所有的系统服务。

查看当前系统的运行级别

```bash
systemctl get-default
```

设置运行级别

```bash
systemctl set-default TARGET.target
```

```bash
chkconfig -- list 
列出chkconfig所管理的服务在每个级别是否自启动》
chkconfig -- level 3 network off/on

```

```bash
systemctl list-unit-files 查看所有服务的自启动状态。
systemctl enable 服务名//设置开机启动
systemctl disable 服务名//关闭服务开机启动
systemctl is-enabled  查看某个服务是否自启动

systemctl status 服务名 查看状态
systemctl stop 服务名
systemctl start 服务名 //临时关闭和开启
```

##### 动态监控进程

```
top -d 秒数  每隔几秒更新，默认是3s
    -u 使top不显示任何闲置或者僵死进程
    -p 通过指定监控进程ID来仅仅监控某个进程的状态。
```

```
netstat 查看网络情况
 -an  按一定顺序排列输出
 -p  显示哪个进程在调用
```

```bash
rpm -qa// 查询安装的所有软件包
rpm -q 软件包名字  //查询是否安装了某个软件包
rpm -qi //查询软件包详细信息
rpm -ql 查询软件包中的文件
rpm -qf 文件全路径名  查询文件所属的软件包
rpm -e 软件包  //删除软件包
```

