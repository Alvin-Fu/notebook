### 消费者的问题
```
[appuser@hostname kafka]$ /usr/local/kafka/bin/kafka-console-consumer --bootstrap-server localhost:9092 --topic test --from-beginning
OpenJDK 64-Bit Server VM warning: If the number of processors is expected to increase from one, then you should configure the number of parallel GC threads appropriately using -XX:ParallelGCThreads=N
[2020-07-21 15:36:08,755] WARN [Consumer clientId=consumer-1, groupId=console-consumer-13172] Connection to node -1 (localhost/127.0.0.1:9092) could not be established. Broker may not be available. (org.apache.kafka.clients.NetworkClient)
```
这个主要是kafka配置中的配置出错需要修改
`listeners=PLAINTEXT://:9092或者advertised.listeners=PLAINTEXT://localhost:9092`

