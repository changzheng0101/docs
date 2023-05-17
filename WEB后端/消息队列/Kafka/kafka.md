通过Broker与之交互，Broker其实就是不同的主机，多个Broker可以组成集群



Topic中有很多Partition，数据其实都放在Partition中，Partition可以跨多台机器，之后由Consumer进行消费。



record是一条消息，会发送到topic的partition中。



**Topic是无序的，但是里面的partition中的数据是有序的。**



如何决定 record发送到哪个分区？

1. record中指定分区
2. 通过record的key进行分区算法找到目标分区（分区器来执行）



