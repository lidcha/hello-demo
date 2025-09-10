## Kafka

1.  Kafka如何保证的高吞吐（顺序写磁盘，零拷贝，批量写）
2.  Kafka的架构（producer, broker, consumer group, partition的主从机制等）
3.  Kafka如何保证消息的顺序
4.  如何保证消息不丢失（ACK机制），如何保证消息不被漏消费或者多消费（幂等性的角度）
5.  如何合理设置partition的数量
6.  Kafka的rebalance，如何避免
7.  Kafka在新版本Kraft架构下和旧版ZK架构的区别
8.  Kafka如何选出Controller和各个分区的leader
9.  高水位和低水位机制
10. 事务是如何保证的
11. 如何处理消息堆积
12. 和其他消息队列的差别
13. raft协议的各种细节（为什么要选举超时，如何避免频繁选举，WAL等）