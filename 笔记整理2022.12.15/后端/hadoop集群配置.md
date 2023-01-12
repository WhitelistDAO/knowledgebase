Hadoop是一个用Java编写的Apache开源框架，允许使用简单的编程模型跨计算机集群分布式处理大型数据集。 Hadoop框架工作的应用程序在跨计算机集群提供分布式存储和计算的环境中工作。 Hadoop旨在从单个服务器扩展到数千个机器，每个都提供本地计算和存储。

- 创建虚拟机：hadoop（创建三台）
- 安装java：sudo apt install openjdk-11-jdk
- 设置静态ip：193.168.50.129
- vim /etc/hostname 修改主机名：hadoop

- vim /etc/hosts，添加：

  192.168.50.129 hadoop

  192.168.50.130 hadoop2

  192.168.50.131 hadoop3

- 克隆两台虚拟机：hadoop2, hadoop3，并修改主机名分别为：hadoop2，hadoop3，设置静态网络：192.168.50.130，192.168.50.131

- 用winscp将hadoop-3.2.2.tar.zp压缩包添加到hadoop中
- 解压hadoop-3.2.2.tar.zp 到/usr/local目录中，并该名为hadoop
- cd /usr/local/hadoop/etc/Hadoop

- 修改workers文件，core-site.xml文件，hdfs-site.xml文件，mapred-site.xml文件，yarn-site.xml文件

- ​    再/home/hadoop 目录运行ssh-keygrn -t rsa命令，四个回车

- ​    压缩/usr/local/hadoop: tar -zcf ~/hadoop.master.tar.gz Hadoop，压缩前删除tmp和logs文件

- 将压缩包hadoop.master.tar.gz hadoop和/home/Hadoop/.ssh/id_ras.pub分发到其他两台主机

- ​    hadoop主机在.ssh目录下cat./id_rsa.pub >> ./authorized_keys，其他两台主机在home目录cat./id_rsa.pub >> ~/.ssh/authorized_keys(没有则，mkdir .ssh)

- ​    解压hadoop压缩包到/usr/local：

  sudo tar -xzvf /home/hadoop1/hadoop.master.tar.gz

  sudo chown -R 用户名 /usr/local/hadoop 

- hadoop初始化：

  在主节点（hadoop）运行：/usr/local/hadoop/bin/hdfs namenode -format

- 启动集群

​    1、cd /usr/local/Hadoop/sbin

​    2、start-dfs.sh

​    3、start-yarn.sh

​    4、`mapred --daemon start historyserver`

配置好集群后可以使用分布式文件系统。HDFS（Hadoop Distributed File System）是hadoop生态系统的一个重要组成部分，是hadoop中的的存储组件，在整个Hadoop中的地位非同一般，是最基础的一部分，因为它涉及到数据存储，MapReduce等计算模型都要依赖于存储在HDFS中的数据。HDFS是一个分布式文件系统，以流式数据访问模式存储超大文件，将数据分块存储到一个商业硬件集群内的不同机器上。