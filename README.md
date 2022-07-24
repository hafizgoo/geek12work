# geek12work

1.（必做）配置 redis 的主从复制，sentinel 高可用，Cluster 集群。

        主从复制 
        启动3个redis容器服务，分别使用到6379、6380、6381端口
        docker run --name redis-6379 -p 6379:6379 -d redis
        docker run --name redis-6380 -p 6380:6379 -d redis
        docker run --name redis-6381 -p 6381:6379 -d redis
        使用redis-cli命令修改redis-6380、redis-6381的主机为172.17.0.2:6379
        slaveof 172.17.0.1 6379
        
        sentinel 高可用
        进入3台redis容器内部进行配置，在容器根目录里面创建sentinel.conf文件
        sentinel monitor master001 172.17.0.2 6379 2
        sentinel down-after-milliseconds master001 30000
        sentinel parallel-syncs master001 1
        sentinel failover-timeout master001 180000
        进入三个容器中执行
        redis-sentinel /sentinel.conf
        关闭master
        docker stop ab54741166e1
        将会有一个salve变为master
        
        Cluster 集群
        准备好集群网络
        docker network create 172.19 --gateway 172.19.0.1 --subnet 172.19.0.0/24
        准备如下6个节点ip
        172.19.0.11,172.19.0.12,172.19.0.13,172.19.0.14,172.19.0.15,172.19.0.16
        准备好对应的6个配置文件
        redis1.conf 等 具体文件在下方
        拉起6个redis容器
        docker run -d --name redis1 --net redis --ip 172.38.0.11 -p 6371:6379  -p 16371:16379  -v /docker-data/redis:/data  -v redis1.conf:/etc/redis/redis.conf redis redis-server /etc/redis/redis.conf
        docker run -d --name redis2 --net redis --ip 172.38.0.12 -p 6372:6379 -p 16372:16379 -v /docker-data/redis:/data  -v redis2.conf:/etc/redis/redis.conf redis redis-server /etc/redis/redis.conf
        docker run -d --name redis3 --net redis --ip 172.38.0.13 -p 6373:6379 -p 16373:16379  -v /docker-data/redis:/data  -v redis3.conf:/etc/redis/redis.conf redis redis-server /etc/redis/redis.conf
        docker run -d --name redis4 --net redis --ip 172.38.0.14 -p 6374:6379 -p 16374:16379  -v /docker-data/redis:/data  -v redis4.conf:/etc/redis/redis.conf redis redis-server /etc/redis/redis.conf
        docker run -d --name redis5 --net redis --ip 172.38.0.15 -p 6375:6379  -p 16375:16379 -v /docker-data/redis:/data  -v redis5.conf:/etc/redis/redis.conf redis redis-server /etc/redis/redis.conf
        docker run -d --name redis6 --net redis --ip 172.38.0.16 -p 6376:6379  -p 16376:16379 -v /docker-data/redis:/data  -v redis6.conf:/etc/redis/redis.conf redis redis-server /etc/redis/redis.conf
        任意进入一个节点，执行如下命令
        redis-cli --cluster create 172.38.0.11:6379 172.38.0.12:6379 172.38.0.13:6379 172.38.0.14:6379 172.38.0.15:6379 172.38.0.16:6379 --cluster-replicas 1
        在当前节用redis终端登入redis节点 即可查看集群信息
   
  [集群所使用的配置文件](https://github.com/hafizgoo/geek12work/tree/main/conf)
         

2.（必做）搭建 ActiveMQ 服务，基于 JMS，写代码分别实现对于 queue 和 topic 的消息生产和消费，代码提交到 github。

   [queue生产者](https://github.com/hafizgoo/geek12work/blob/main/activemq/src/main/java/com/hafizgoo/activemq/AppProducer.java)

   [queue消费者](https://github.com/hafizgoo/geek12work/blob/main/activemq/src/main/java/com/hafizgoo/activemq/AppConsumer.java)

   [topic生产者和消费者](https://github.com/hafizgoo/geek12work/blob/main/activemq/src/main/java/com/hafizgoo/activemq/ActivemqApplication.java)
