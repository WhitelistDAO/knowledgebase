###etcd操作


####增删改查
增加或者修改key

	PUT key val

查找key

	GET key
	范围查找，范围是左闭右开区间，key按字典序查找
	GET keyfrom keyend
	前缀索引
	GET --prefix key

删除key

	DEL key

####监听
用来实现监听和推送服务

	WATCH key
	WATCH --prefix key

当监听的key发生变化（增删改）时，终端会输出这个变化的操作，注意不响应查找操作。

####事务
用于分布式锁以及leader选举，保证多个操作的原子性，确保多个节点数据读写的一致性。

	-i表示交互式操作
	txn -i

执行命令后，根据提示输入的三段指令

* compare：相当于if
* success requests：相当于then，上面的条件判断为真，则执行这里
* failure request：相当于else，上面的条件为假时执行

####租约
是对expire的优化。在etcd中，大量的key设置expire的话，效率会很低。在实际使用过程中发现，很多key的过期时间相等，也就是说，同时过期。针对这种情况，v3版本做了一个优化，设置一个对象，挂接多个key共享一个过期实体，这个实体就被称为租约（lease）。租约过期后，挂接在上面的所有key都会被删除。这样一来，把相同过期时间的key挂接到同一租约上，不同过期时间的key挂接到不同租约上，就把对key的过期管理转变为了对租约的管理。

	lease grant 60 # 创建一个租约，这里参数60表示60秒后租约过期
	lease keep-alive  # 续约，刷新当前租约的时间，重新计时
	lease list  # 枚举所有的租约
	lease revoke  # 销毁租约
	lease timetolive  # 获取租约信息

创建租约后，将key挂到租约上

	等号后面是创建这个租约使返回的id值
	put hello world --lease=694d7d17eaab280f

此外可以通过续约和监听（watch）实现服务注册发现。前提是客户端对etcd中的某个key进行操作，这个key是挂接在某个租约上的。具体做法：

1. 启动一个服务，监听这个key
2. 客户端发送心跳包，会续约，不会删除这个key
3. 如果超过指定时间未收到心跳包，续约过期，这个key就删除了
4. watch服务监听到了delete操作，发现节点删除。此外，如果有新服务注册进来，监听服务也能感知到。
