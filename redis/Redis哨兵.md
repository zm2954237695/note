#### Redis哨兵

##### 哨兵的作用

- 监控：Sentinel会不断检查master和slave是否按预期工作
- 自动故障恢复：如果master故障，Sentinel会将slave提升为master。当故障实例恢复后也以新的master为主
- 通知：Sentinel充当Redis客户端的服务发现来源，当集群发送故障转移时，会将最新信息推送给Redis的客户端。

##### 服务状态监控

Sentinel基于心跳机制监测服务状态，每隔1s向集群内的每个实例发送Ping命令：

- 主观下线：如果某sentinel节点发现某实例未在规定时间响应，则认为该实例为主观下线
- 客观下线：若超过指定数量的sentine都认定该实例主观下线，则该实例客观下线。

##### 选举新的master

一旦发现master故障，sentinel需要在slave中选择一个作为新的master，选择依据是这样的：

- 首先会判断slave节点和master节点断开时间长短，如果超过指定值，则会排除该节点
- 然后判断slave节点的slave-priority值，越小优先级越高，如果是0则永不参与选举
- 如果slave-priority一样，则判断slave节点的offset值，越大则说明数据越新，优先级越高
- 最高是判断slave节点的运行id大小，id越小优先级越高。

##### 如何实现故障转移？

当选中了其中一个slave成为新的master后，故障转移的步骤如下：

- sentinel给备选的slave节点发送slaveof no one命令，让该节点成为新的master
- sentinel给所有其它slave发送slave of命令，让这些slave成为新的master的从节点，开始从master上同步数据
- 最后，sentinel将故障节点标记为slave，当故障节点恢复后自动成为新的slave节点。

