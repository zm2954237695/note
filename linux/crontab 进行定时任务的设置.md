crontab 进行定时任务的设置

```bash
crontab -e 编辑crontab定时任务
crontab -l 查询crontab任务
crontab -r 删除当前用户的所有crontab任务
```

使用crontab -e如下例子：

```bash
*/1 * * * * ls -l /etc/ >/tmp/to.ext
```

5个占位符意义如下：

```
第一个* 表示每小时中的第几分钟  0-59

第二个* 表示每天中的第几小时  0-23

第三个* 表示每月中的第几天  1-31

第四个* 表示每年中的第几个月 1-12

第五个* 表示每周中的星期几  0-7 （0,7都是星期天)
```

at + 时间  在某个时间点执行任务 只执行一次。

![image-20211219114950082](C:\Users\逐梦\AppData\Roaming\Typora\typora-user-images\image-20211219114950082.png)

查看所有设备的挂载情况

```bash
lsblk  
lsblk -f
```



