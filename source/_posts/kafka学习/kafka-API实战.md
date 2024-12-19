---
layout: posts
title: kafka_API实战
date: 2022-10-29 15:41:53
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "kafka"
tags:
- "kafka"
---

简介 <!--more-->
# python实现

​		网上有很多kafka的java 操作的教程、文档，但是我这里想要把学习笔记统一成python的，简单易读人生苦短嘛。

​		用python操作kafka 有两个选择，一个是pykafka，另一个是kafka- python，pykafka 很久没有更新了，所以咱们用 kafka- python。

​		以下是 kafka- python的文档，看起来满屏的英文其实还是挺好读的。

https://kafka-python.readthedocs.io/en/master/apidoc/KafkaConsumer.html

​		知乎中的前人的总结，可以和kafka- python的文档 配合着看。

https://zhuanlan.zhihu.com/p/279784873?utm_source=wechat_session&utm_medium=social&utm_oi=1082949711211810816&utm_campaign=shareopn

​		

## 生产者与消费者

### 生产者

```python
pip install kafka-python
```

```python
# coding:utf8
from kafka import KafkaProducer
from kafka.errors import kafka_errors
import traceback
import json

def create_producer():
  	# 假设生产的消息为键值对（不是一定要键值对），且序列化方式为json
    producer = KafkaProducer(
        bootstrap_servers=['node01:9092'],
        acks=1,
        key_serializer=lambda k: json.dumps(k).encode(),
        value_serializer=lambda v: json.dumps(v).encode()
    )
    for i in range(0,10):
      	# send（）return 一个future metadata类型。
        future = producer.send(
            'test-topic',
            key='count_num',  # 同一个key值，会被送至同一个分区
            value=str(i),
            partition=2   # 向分区2发送消息
        )
        print('send {}'.format(str(i)))
        try:
          	# 有 get() 就会变成同步操作，一定要等反馈才会发送下一条消息
            # 无 get() 是异步操作，无需等待反馈直接发送消息
            # get() 会触发真实_send(),像把锁，锁住下个真实的_send()
            # get() 返回的是recordmetadata类型，可以看数据到底是发送到了哪个分区，偏移量是多少。
            future.get(timeout=10)  # 监控是否发送成功   
            print( 'producer send %d is ok! '%(i))
            print(type(future))  # FutureRecordMetadata 类型
        except kafka_errors:
            traceback.format_exc()
		producer.close()
if __name__ == '__main__':
    create_producer()
```

`kafka-consumer-groups.sh --bootstrap-server node01:9092 --list`

`kafka-consumer-groups.sh --bootstrap-server node01:9092 --describe --group test-consumer-group`

​		可以看见生产者发送完数据后，消费者从各个partition中消费完offset偏移量的变化。

### 带回调函数的生产者

python [kafka](https://so.csdn.net/so/search?q=kafka&spm=1001.2101.3001.7020) 生产者发送数据的三种方式:

​		1、sync同步发送（等）

​		2、asyn异步发送（不等）

​		3、异步+回调处理（综合前两者）较快、不丢数据

​				异步+回调函数模式，send方法发送消息的同时，send的返回对象FutureRecordMetadata 指定一个回调函数，服务器在返回响应时会调用该回调函数，通过回调函数能够对异常情况进行处理，当调用了回调函数时，只有回调函数执行完毕生产者才会结束，否则一直会阻塞。

```python
# 比起java 实现回调函数 ，要在send（）参数内 new 一个callback对象并重写方法实现接口，python的 写法更加易读易理解逻辑，python 对send（）的返回结果 追加一个callback。
# 写代码时记得常用 ctrl+command+点击 在idea 中查看源码，学会使用没见过的方法。

# coding:utf8
import json
from kafka import KafkaProducer

class Producer():
    def __init__(self,bootstrap_servers,topic,acks):
        self.producer = KafkaProducer(
            bootstrap_servers = bootstrap_servers,
            value_serializer = lambda v: json.dumps(v).encode(),
            acks = acks)
        self.topic = topic

    # 同步发送 数据
    def sync_producer(self,data_list):
        for data in data_list:
            future = self.producer.send(self.topic,data)
            record_metadata = future.get(timeout=10)
            partition = record_metadata.partition
            offset = record_metadata.offset
            print('save success,partition:{},offset:{}'.format(partition,offset))

    # 异步发送数据
    def asyn_producer(self,data_list):
        for data in data_list:
            self.producer.send(self.topic,data)
        self.producer.flush()  # 批量提交，触发真实_send()。多线程flush的话，一个flush在阻塞状态，不影响别的线程提交。

    # 异步发送数据 + 用回调函数 调用指定方法。
    def asyn_producer_callback(self,data_list):
        for data in data_list:
            self.producer.send(self.topic,data).\
                add_callback(self.send_ok).\
                add_errback(self.send_no_ok)
        self.producer.flush()

    # this mathod must have args! otherwise the mathod no run.
    # 被回调函数调用的方法一定要有参数，此参数就是返回的recordmetadata类型数据，可以直接看partition和offset 等信息。此参数相当于同步发送时用get()后的返回值，可以看数据到底是发送到了哪个分区，偏移量是多少。
    def send_ok(self,record_meta):
        """异步发送成功回调函数"""
        print('asyn_callback save sucess')
        print(type(record_meta),'\n',
              'partition:{},offset:{}'.format(record_meta.partition,record_meta.offset))

    def send_no_ok(self, *args, **kwargs):
        """异步发送错误回调函数"""
        print('asyn_callback save error')

    def close_proceder(self):
        try:
            self.producer.close()
        except:
            print('close producer false')

if __name__=='__main__':
    data_list = [1,3,5,7,9]
		
    # 同步发送
    # P = Producer(topic='test-topic',bootstrap_servers='node01:9092',acks=-1)
    # P.sync_producer(data_list)

    # 异步发送
    # P = Producer(topic='test-topic',bootstrap_servers='node01:9092',acks=0)
    # P.asyn_producer(data_list)

    # 异步 + 回调
    P = Producer(topic='test-topic',bootstrap_servers='node01:9092',acks=1)
    P.asyn_producer_callback(data_list)
		
    # 关闭producer。 
    P.close_proceder()

```

```python
# output
asyn_callback save sucess
<class 'kafka.producer.future.RecordMetadata'> 
 partition:1,offset:36
asyn_callback save sucess
<class 'kafka.producer.future.RecordMetadata'> 
 partition:1,offset:37
asyn_callback save sucess
<class 'kafka.producer.future.RecordMetadata'> 
 partition:1,offset:38
asyn_callback save sucess
<class 'kafka.producer.future.RecordMetadata'> 
 partition:0,offset:107
asyn_callback save sucess
<class 'kafka.producer.future.RecordMetadata'> 
 partition:2,offset:161
```

消费者组可以查看偏移量情况，可见current-offset偏移量正好接上。

`kafka-consumer-groups.sh --bootstrap-server node01:9092 --describe --group test-consumer-group`

![截屏2022-04-27 20.01.33](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-27%2020.01.33-7029089.jpg)

​		

### 消费者	

​		idea中点击源码可见KafkaConsumer继承了一个迭代器。这样就好理解了。	

```python
# coding:utf8
from kafka import KafkaConsumer,TopicPartition
import json

class Consumer():
    # 看源码可知 KafkaConsumer 参数 topics 要后期订阅。
    def __init__(self,topics,bootstrap_servers,group_id,consumer_id):
        self.consumer = KafkaConsumer(
            bootstrap_servers=bootstrap_servers,
            group_id = group_id)
        self.topics = topics
        self.consumer_id = consumer_id

    def simple_consumer(self):
        self.consumer.subscribe(self.topics)
        for message in self.consumer:
            print('consumer_id={}-->receive,key:{},value:{}'.format(self.consumer_id,message.key,message.value))

if __name__=='__main__':
  
    consumer = Consumer(topics = 'consumer_test',
                        bootstrap_servers="node01:9092,node02:9092,node03:9092",
                        group_id='consumer_group_test',
                        consumer_id=1)
    print(type(consumer),'\n',consumer)
    consumer.simple_consumer()
```

​		在node02开一个窗口版的kafka生产者

​		`kafka-console-producer.sh --broker-list node01:9092,node02:9092,node03:9092 --topic consumer_test`

​		输入 消息内容。ok1 ok2 ... ok11 

​		在node01内赋值上述脚本两份，一份consumer_id=1，一份consumer_id=2，实现消费者组。两份脚本同时运行上面消费者代码，可见如下：

![截屏2022-05-02 12.03.58](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-05-02%2012.03.58.jpg)

![截屏2022-05-02 12.04.13](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-05-02%2012.04.13.jpg)



### 升级版消费者

```python
# coding:utf8
import time

from kafka import KafkaConsumer,TopicPartition
import json

class Consumer():
    # 看源码可知 KafkaConsumer 参数 topics不是直接赋值，而是后期订阅或者分配。
    def __init__(self,bootstrap_servers,group_id,consumer_id):
        self.consumer = KafkaConsumer(
            bootstrap_servers=bootstrap_servers,
            group_id = group_id,
            enable_auto_commit = False) # 关闭自动提交消费者数据到集群，集群没接到commit就不会把position下次消费的offset指针往后移。手动提交的好处之一就是 避免数据重读，在自动提交情况下一旦读取数据但是还没提交，此时partition挂掉了，很可能导致下次消费者重读一遍，而手动提交如果发现partition挂掉会抛出异常，阻塞，一直试到发送成功。
        self.consumer_id = consumer_id
		
    # 定单个topic的分区，分配给消费者，然后seek指定offset偏移量，一个一个顺序消费。
    def user_defined_offset_consume(self,topic,partition):
        tp = TopicPartition(topic,partition)
        print(type(tp),tp)
        # it is not possible to use both manual partition assignment with assign()
        # and group assignment with subscribe().
        # 订阅和分配只能二选一。
        self.consumer.assign([tp])
        for i in range(12,30):
            self.consumer.seek(tp,i)
            consumer_data = next(self.consumer) # 注意：使用next后，偏移量自动+1
            print(consumer_data)
            self.consumer.commit()
		
		# 指定多个topic分区，分配给消费者，用position找到消费者下次即将消费的offset位置，再用seek指定到此位置开始消费。
    # 但是要注意到：consumer.assign()多个分区, seek 再指定的情况，会让consumer 随机一个分区逐个消费数据，直到最新数据，然后随机切换到另一个分区从指定位置消费,不会一直在一个分区消费！！！
    # 注意：会在多个分区中切换着消费，直到生产者的end—offset位置，如果不断有新消息到不同分区，还会不停的切换着分区消费！！！
    # 错误的写法结果：在循环到第一个分区时，就因为调用了consumer消费，在不同分区（seek的起始位置～生产者最新写入的offset位置）中消费，直到消费到各个分区的生产者最新写入的offset位置。
    # 正确的写法结果：循环完三个分区，显示完了各个分区消费到哪个offset位置了，再调用consumer消费，在不同分区（seek的起始位置～生产者最新写入的offset位置）中消费，直到消费到各个分区的生产者最新写入的offset位置。
    def from_next_offset_consume(self,topic):
        tps = [TopicPartition(topic,p) for p in self.consumer.partitions_for_topic(topic)]
        self.consumer.assign(tps)
        for tp in tps:
            next_offset = self.consumer.position(tp)
            print('分区 :{} ,的将要消费的offset是{}'.format(tp.partition,next_offset))
            self.consumer.seek(tp,next_offset)
            # 错误写法
            # for message in self.consumer:
            #     print(message)
            #     self.consumer.commit()
        print('~~~~~~~~~~~~~~~~~~~~~')
        # 正确写法。
        for message in self.consumer:
            print(message)
						self.consumer.commit()

    def simple_consumer(self,topic):
        self.consumer.subscribe(topic)
        for message in self.consumer:
            print('consumer_id={}-->receive,key:{},value:{}'.format(self.consumer_id,message.key,message.value))
						self.consumer.commit()
            
if __name__=='__main__':
    consumer = Consumer(bootstrap_servers="node01:9092,node02:9092,node03:9092",
                        group_id="consumer_group_multifunction_test",
                        consumer_id=1)
    # consumer.simple_consumer(topic='consumer_test')
    # consumer.user_defined_offset_consume(topic='consumer_test',partition=0)
    consumer.from_next_offset_consume(topic='consumer_test')

```

```python
# output 指定单个topic的分区，分配给消费者，然后seek指定offset偏移量，一个一个顺序消费。
# def user_defined_offset_consume(self,topic,partition):consumer.user_defined_offset_consume(topic='consumer_test',partition=0)
<class 'kafka.structs.TopicPartition'> TopicPartition(topic='consumer_test', partition=0)
ConsumerRecord(topic='consumer_test', partition=0, offset=12, timestamp=1651463940201, timestamp_type=0, key=None, value=b'ok2', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=3, serialized_header_size=-1)
ConsumerRecord(topic='consumer_test', partition=0, offset=13, timestamp=1651464157363, timestamp_type=0, key=None, value=b'ok5', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=3, serialized_header_size=-1)
ConsumerRecord(topic='consumer_test', partition=0, offset=14, timestamp=1651464161278, timestamp_type=0, key=None, value=b'ok7', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=3, serialized_header_size=-1)
ConsumerRecord(topic='consumer_test', partition=0, offset=15, timestamp=1651470871128, timestamp_type=0, key=None, value=b'ok12', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=4, serialized_header_size=-1)
ConsumerRecord(topic='consumer_test', partition=0, offset=16, timestamp=1651476914181, timestamp_type=0, key=None, value=b'ok14', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=4, serialized_header_size=-1)
ConsumerRecord(topic='consumer_test', partition=0, offset=17, timestamp=1651478269618, timestamp_type=0, key=None, value=b'ok17', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=4, serialized_header_size=-1)
ConsumerRecord(topic='consumer_test', partition=0, offset=18, timestamp=1651478733176, timestamp_type=0, key=None, value=b'ok21', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=4, serialized_header_size=-1)
ConsumerRecord(topic='consumer_test', partition=0, offset=19, timestamp=1651478792011, timestamp_type=0, key=None, value=b'ok24', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=4, serialized_header_size=-1)
ConsumerRecord(topic='consumer_test', partition=0, offset=20, timestamp=1651479887199, timestamp_type=0, key=None, value=b'ok26', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=4, serialized_header_size=-1)
ConsumerRecord(topic='consumer_test', partition=0, offset=21, timestamp=1651480351104, timestamp_type=0, key=None, value=b'ok3', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=3, serialized_header_size=-1)

```

```python 
# output 
# def from_next_offset_consume(self,topic):
# 正确写法结果。
分区 :0 ,的将要消费的offset是20
分区 :1 ,的将要消费的offset是21
分区 :2 ,的将要消费的offset是17
~~~~~~~~~~~~~~~~~~~~~
ConsumerRecord(topic='consumer_test', partition=0, offset=20, timestamp=1651479887199, timestamp_type=0, key=None, value=b'ok26', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=4, serialized_header_size=-1)
ConsumerRecord(topic='consumer_test', partition=0, offset=21, timestamp=1651480351104, timestamp_type=0, key=None, value=b'ok3', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=3, serialized_header_size=-1)
ConsumerRecord(topic='consumer_test', partition=1, offset=21, timestamp=1651479980961, timestamp_type=0, key=None, value=b'ok38', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=4, serialized_header_size=-1)
ConsumerRecord(topic='consumer_test', partition=1, offset=22, timestamp=1651480349866, timestamp_type=0, key=None, value=b'ok2', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=3, serialized_header_size=-1)
ConsumerRecord(topic='consumer_test', partition=1, offset=23, timestamp=1651480522299, timestamp_type=0, key=None, value=b'ok4', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=3, serialized_header_size=-1)
ConsumerRecord(topic='consumer_test', partition=2, offset=17, timestamp=1651479870173, timestamp_type=0, key=None, value=b'ok25', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=4, serialized_header_size=-1)
ConsumerRecord(topic='consumer_test', partition=2, offset=18, timestamp=1651479910881, timestamp_type=0, key=None, value=b'ok27', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=4, serialized_header_size=-1)
ConsumerRecord(topic='consumer_test', partition=2, offset=19, timestamp=1651480348243, timestamp_type=0, key=None, value=b'ok1', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=3, serialized_header_size=-1)

```

```python
# output 
# def from_next_offset_consume(self,topic):
# 错误写法结果。
分区 :0 ,的将要消费的offset是20
ConsumerRecord(topic='consumer_test', partition=2, offset=17, timestamp=1651479870173, timestamp_type=0, key=None, value=b'ok25', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=4, serialized_header_size=-1)
ConsumerRecord(topic='consumer_test', partition=2, offset=18, timestamp=1651479910881, timestamp_type=0, key=None, value=b'ok27', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=4, serialized_header_size=-1)
ConsumerRecord(topic='consumer_test', partition=2, offset=19, timestamp=1651480348243, timestamp_type=0, key=None, value=b'ok1', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=3, serialized_header_size=-1)
ConsumerRecord(topic='consumer_test', partition=1, offset=21, timestamp=1651479980961, timestamp_type=0, key=None, value=b'ok38', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=4, serialized_header_size=-1)
ConsumerRecord(topic='consumer_test', partition=1, offset=22, timestamp=1651480349866, timestamp_type=0, key=None, value=b'ok2', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=3, serialized_header_size=-1)
ConsumerRecord(topic='consumer_test', partition=1, offset=23, timestamp=1651480522299, timestamp_type=0, key=None, value=b'ok4', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=3, serialized_header_size=-1)
ConsumerRecord(topic='consumer_test', partition=0, offset=20, timestamp=1651479887199, timestamp_type=0, key=None, value=b'ok26', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=4, serialized_header_size=-1)
ConsumerRecord(topic='consumer_test', partition=0, offset=21, timestamp=1651480351104, timestamp_type=0, key=None, value=b'ok3', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=3, serialized_header_size=-1)

```

​		运行前，没有提交时，用消费者组看消费情况

`kafka-consumer-groups.sh --bootstrap-server node01:9092 --describe --group consumer_group_multifunction_test`

​		运行完，提交之后，再查看消费情况。

![截屏2022-05-02 20.48.36](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-05-02%2020.48.36.jpg)

![截屏2022-05-02 20.58.11](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-05-02%2020.58.11.jpg)

​		

### 计算kafka堆积剩余量	

```python
# conding:utf8
from kafka import KafkaConsumer,TopicPartition

class view_accumulation():
    def __init__(self,topic,bootstrap_servers,group_id):
        self.topic = topic
        self.group_id = group_id
        self.consumer = KafkaConsumer(bootstrap_servers=bootstrap_servers,group_id=self.group_id)
        self.tps = [TopicPartition(self.topic,p) for p in self.consumer.partitions_for_topic(self.topic)]
        self.calculation_allowance()

    def producer_write_offset(self):
        p_offset = self.consumer.end_offsets(self.tps)
        print(p_offset) # {TopicPartition(topic='consumer_test', partition=0): 23, TopicPartition(topic='consumer_test', partition=1): 25, TopicPartition(topic='consumer_test', partition=2): 21}
        p_offset = [(key.partition,p_offset[key]) for key in p_offset.keys()]
        print(p_offset) # [(0, 23), (1, 25), (2, 21)]
        return p_offset

    def consumer_read_offset(self):
        c_offset = [(tp.partition,self.consumer.committed(tp)) for tp in self.tps] # return None if there was no prior commit.
        print('\n',c_offset)  #  [(0, 22), (1, 24), (2, 20)]
        return c_offset

    def calculation_allowance(self):
        sum_of_p_offset = sum([x[1] for x in self.producer_write_offset()])
        sum_of_c_offset = sum([x[1] if x[1]!=None else 0 for x in self.consumer_read_offset() ])
        allowance = sum_of_p_offset-sum_of_c_offset
        print('\n kafka accumulate space except_group_id {} is: \n{}'.format(self.group_id,allowance))
        return allowance

if __name__=='__main__':
    v = view_accumulation(bootstrap_servers="node01:9092,node02:9092,node03:9092",
                          topic='consumer_test',
                          group_id="consumer_group_multifunction_test")
```

 		我在生产者输入6个消息调用上述计算kafka 堆积量的脚步，可见。

![截屏2022-05-02 22.31.40](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-05-02%2022.31.40.jpg)



### 拦截器

​		kafka-python 并没有实现 interceptor拦截器。拦截器主要是做producer的 send 之前和callback回调 之后的数据过滤或者加工的，python实际上很擅长干数据过滤加工相关的活，不用interceptor 也是可以实现拦截器的功能的。











# 读kafka-python源码（kafkaproducer）

​		所有细节都看明白不容易，但是一些模块化很强的地方是容易理解的。在idea中读源码可以用快捷键查 `ctrl + command + 点击` 看源码；`ctrl+command+h` 看继承关系；`alt+上下键` 方法上下切换；`alt+左右键` idea中模块左右切换；`选中类+alt+7 ` 还能看见类下所有方法...等等。也可以不用idea直接看 kafka- python的文档，我还是喜欢idea读一些。

## 读源码前奏：更改默认发送去哪个分区的算法

​		ctrl+command+点击 kafkaproducer() ,跳转至kafka.py模块，点击 self.config 可见一个dict 存放了大量待调用属性和方法；

​		ctrl + f  搜索 partition 可以见 default—config 中有 如下键值对。以及筛选方法。

![截屏2022-04-28 21.51.21](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-28%2021.51.21.jpg)

![截屏2022-04-28 22.00.13](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-28%2022.00.13.jpg)

​		ctrl+command+点击  defaultpartitioner() ,跳转至 default.py 模块。

![截屏2022-04-28 21.52.30](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-28%2021.52.30.jpg)

​		读一读就明白了，原来这里是决定发送到哪一个分区的算法，defaultpartitioner  类 内部有 一个被类方法修饰过的 `__call__()`方法，此方法是将原本不可以调用的类对象，变成可以直接调用的对象（把类变得和方法一样可以调用），并且每一次调用，会自动运行`__call__()`下的代码。

​		这样一来，如果我们把`__call__()` 方法下的内容改为 `return all_partitions[0]`  ，以后只要不在send()加partition参数，都会调用此修改后的方法把消息发送到 topic的 0号分区。

![截屏2022-04-28 22.08.15](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-28%2022.08.15.jpg)

​		补充一点：idea中不能直接修改源码，会提示revert changes修改被还原，cd 到指定文件夹，chmod 更改模块的权限，用vim 进行编辑。	

```shell
cd /opt/anaconda3/lib/python3.8/site-packages/kafka/partitioner/

# 在kafka中打开一个消费者组，检查消费者组的消费情况。
# 发现发来的消息确实全部发送进了 partition 0 分区，消费者都从这个分区内拿数据。
kafka-consumer-groups.sh --bootstrap-server node01:9092 --describe --group console-consumer-44048
```

![截屏2022-04-29 10.11.53](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-29%2010.11.53.jpg)

![截屏2022-04-29 10.12.29](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-29%2010.12.29.jpg)

​		消费者组都是从partition0 中消费，offset变更都发生在partition0。



## 正式读源码

​		ctrl + command + 点击 KafkaProducer![截屏2022-04-28 16.21.52](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-28%2016.21.52.jpg)

​		可见kafkaproducer 的构造方法。

![截屏2022-04-28 16.23.12](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-28%2016.23.12.jpg)

​		先复制一份默认配置文件到self.config, 里面长这样。

![截屏2022-04-28 16.47.30](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-28%2016.47.30.jpg)

​		收集参数 **configs ，把我们输入进来的 参数变成字典形式的configs。

​		遍历默认配置文件的key，看看哪些被我们用到了。

​		用到了哪些key，就把默认的self.config 相应的字典键值对修改成我们输入的内容。		![截屏2022-04-28 17.43.08](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-28%2017.43.08.jpg)

​		如果 `__init__`构造函数传入的参数**configs 被上一步 遍历完，不断的pop弹出 后，还有未弹出的 字典 键值对，我们就用断言抛出 Unrecognized configs:。

![截屏2022-04-28 17.57.47](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-28%2017.57.47.jpg)

​		如果client_id的值是空的，就在 `kafka-python-producer-` 后面自增一个整数。

![截屏2022-04-28 18.02.38](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-28%2018.02.38.jpg)

​		配置metrics指标标签，设置好最大阻塞时间等信息，调用kafkaclient 生成client信息。

![截屏2022-04-28 18.10.58](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-28%2018.10.58.jpg)

----------------------------------------------------------------------------------------------------------

------------------------------------------------分叉阅读开始---------------------------------------

​		我们好奇Metrics() 是什么，点进去看。

```python
from __future__ import absolute_import

import logging
import sys
import time
import threading

from kafka.metrics import AnonMeasurable, KafkaMetric, MetricConfig, MetricName
from kafka.metrics.stats import Sensor

logger = logging.getLogger(__name__)


class Metrics(object):
    def __init__(self, default_config=None, reporters=None,
                 enable_expiration=False):
        
        self._lock = threading.RLock()
        self._config = default_config or MetricConfig()
        self._sensors = {}
        self._metrics = {}
        self._children_sensors = {}
        self._reporters = reporters or []
        for reporter in self._reporters:
            reporter.init([])
            
# 开启一个守护进程，每隔30秒就run一下。
        if enable_expiration:
            def expire_loop():
                while True:
                    # delay 30 seconds
                    time.sleep(30)
                    self.ExpireSensorTask.run(self)
            metrics_scheduler = threading.Thread(target=expire_loop)
            # Creating a daemon thread to not block shutdown
            metrics_scheduler.daemon = True
            metrics_scheduler.start()

        self.add_metric(self.metric_name('count', 'kafka-metrics-count',
                                         'total number of registered metrics'),
                        AnonMeasurable(lambda config, now: len(self._metrics)))

    @property
    def config(self):
        return self._config

    @property
    def metrics(self):
        """
        Get all the metrics currently maintained and indexed by metricName
        """
        return self._metrics

    def metric_name(self, name, group, description='', tags=None):
        
        combined_tags = dict(self.config.tags)
        combined_tags.update(tags or {})
        return MetricName(name, group, description, combined_tags)

    def get_sensor(self, name):
        """
        Get the sensor with the given name if it exists

        Arguments:
            name (str): The name of the sensor

        Returns:
            Sensor: The sensor or None if no such sensor exists
        """
        if not name:
            raise ValueError('name must be non-empty')
        return self._sensors.get(name, None)

    def sensor(self, name, config=None,
               inactive_sensor_expiration_time_seconds=sys.maxsize,
               parents=None):
        """
        Get or create a sensor with the given unique name and zero or
        more parent sensors. All parent sensors will receive every value
        recorded with this sensor.

        Arguments:
            name (str): The name of the sensor
            config (MetricConfig, optional): A default configuration to use
                for this sensor for metrics that don't have their own config
            inactive_sensor_expiration_time_seconds (int, optional):
                If no value if recorded on the Sensor for this duration of
                time, it is eligible for removal
            parents (list of Sensor): The parent sensors

        Returns:
            Sensor: The sensor that is created
        """
        sensor = self.get_sensor(name)
        if sensor:
            return sensor

        with self._lock:
            sensor = self.get_sensor(name)
            if not sensor:
              # 点进去可以看sensor.py 模块的内容。
                sensor = Sensor(self, name, parents, config or self.config,
                                inactive_sensor_expiration_time_seconds)
                self._sensors[name] = sensor
                if parents:
                    for parent in parents:
                        children = self._children_sensors.get(parent)
                        if not children:
                            children = []
                            self._children_sensors[parent] = children
                        children.append(sensor)
                logger.debug('Added sensor with name %s', name)
            return sensor

    def remove_sensor(self, name):
        """
        Remove a sensor (if it exists), associated metrics and its children.

        Arguments:
            name (str): The name of the sensor to be removed
        """
        sensor = self._sensors.get(name)
        if sensor:
            child_sensors = None
            with sensor._lock:
                with self._lock:
                    val = self._sensors.pop(name, None)
                    if val and val == sensor:
                        for metric in sensor.metrics:
                            self.remove_metric(metric.metric_name)
                        logger.debug('Removed sensor with name %s', name)
                        child_sensors = self._children_sensors.pop(sensor, None)
            if child_sensors:
                for child_sensor in child_sensors:
                    self.remove_sensor(child_sensor.name)

    def add_metric(self, metric_name, measurable, config=None):
        """
        Add a metric to monitor an object that implements measurable.
        This metric won't be associated with any sensor.
        This is a way to expose existing values as metrics.

        Arguments:
            metricName (MetricName): The name of the metric
            measurable (AbstractMeasurable): The measurable that will be
                measured by this metric
            config (MetricConfig, optional): The configuration to use when
                measuring this measurable
        """
        # NOTE there was a lock here, but i don't think it's needed
        metric = KafkaMetric(metric_name, measurable, config or self.config)
        self.register_metric(metric)

    def remove_metric(self, metric_name):
        """
        Remove a metric if it exists and return it. Return None otherwise.
        If a metric is removed, `metric_removal` will be invoked
        for each reporter.

        Arguments:
            metric_name (MetricName): The name of the metric

        Returns:
            KafkaMetric: the removed `KafkaMetric` or None if no such
                metric exists
        """
        with self._lock:
            metric = self._metrics.pop(metric_name, None)
            if metric:
                for reporter in self._reporters:
                    reporter.metric_removal(metric)
            return metric

    def add_reporter(self, reporter):
        """Add a MetricReporter"""
        with self._lock:
            reporter.init(list(self.metrics.values()))
            self._reporters.append(reporter)

    def register_metric(self, metric):
        with self._lock:
            if metric.metric_name in self.metrics:
                raise ValueError('A metric named "%s" already exists, cannot'
                                 ' register another one.' % (metric.metric_name,))
            self.metrics[metric.metric_name] = metric
            for reporter in self._reporters:
                reporter.metric_change(metric)

    class ExpireSensorTask(object):
        """
        This iterates over every Sensor and triggers a remove_sensor
        if it has expired. Package private for testing
        """
        @staticmethod
        def run(metrics):
            items = list(metrics._sensors.items())
            for name, sensor in items:
                # remove_sensor also locks the sensor object. This is fine
                # because synchronized is reentrant. There is however a minor
                # race condition here. Assume we have a parent sensor P and
                # child sensor C. Calling record on C would cause a record on
                # P as well. So expiration time for P == expiration time for C.
                # If the record on P happens via C just after P is removed,
                # that will cause C to also get removed. Since the expiration
                # time is typically high it is not expected to be a significant
                # concern and thus not necessary to optimize
                with sensor._lock:
                    if sensor.has_expired():
                        logger.debug('Removing expired sensor %s', name)
                        metrics.remove_sensor(name)

    def close(self):
        """Close this metrics repository."""
        for reporter in self._reporters:
            reporter.close()

        self._metrics.clear()

```

​		我们好奇这个kafkaclient（）是什么 点进去可见

![截屏2022-04-28 18.50.16](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-28%2018.50.16.jpg)

![截屏2022-04-28 18.51.00](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-28%2018.51.00.jpg)

​		kafkaclient（）里面是包含 clustermetadata 集群元数据的，我们又好奇clustermetadata是什么，再点开，可见kafka集群的信息都被包含在其中了。

![截屏2022-04-28 18.52.31](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-28%2018.52.31.jpg)

![截屏2022-04-28 18.52.58](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-28%2018.52.58.jpg)

------------------------------------------------分叉阅读结束---------------------------------------

----------------------------------------------------------------------------------------------------------

​		选压缩类型

![截屏2022-04-28 18.56.36](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-28%2018.56.36.jpg)

​		接下来是比较关键的部分

![截屏2022-04-29 18.29.16](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-29%2018.29.16.jpg)

```python
        message_version = self._max_usable_produce_magic()
        self._accumulator = RecordAccumulator(message_version=message_version, metrics=self._metrics, **self.config)
        self._metadata = client.cluster
        guarantee_message_order = bool(self.config['max_in_flight_requests_per_connection'] == 1)
        self._sender = Sender(client, self._metadata,
                              self._accumulator, self._metrics,
                              guarantee_message_order=guarantee_message_order,
                              **self.config)
        self._sender.daemon = True
        self._sender.start()
        self._closed = False

        self._cleanup = self._cleanup_factory()
        atexit.register(self._cleanup)
        log.debug("Kafka producer started")
```

​		我们看看self._max_usable_produce_magic() 是个啥。嗷，就是确定消息版本。

![截屏2022-04-29 18.31.31](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-29%2018.31.31.jpg)

​		我们看看recordaccumulator 是个啥。

![截屏2022-05-01 13.08.56](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-05-01%2013.08.56.jpg)

![截屏2022-05-01 13.09.16](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-05-01%2013.09.16.jpg)		这个recordaccumulator 累加器中有一个方法append（)值得读一遍。它创造了一个双端队列dq。

```python
    def append(self, tp, timestamp_ms, key, value, headers, max_time_to_block_ms,
               estimated_size=0):
        """Add a record to the accumulator, return the append result.

        The append result will contain the future metadata, and flag for
        whether the appended batch is full or a new batch is created

        Arguments:
            tp (TopicPartition): The topic/partition to which this record is
                being sent
            timestamp_ms (int): The timestamp of the record (epoch ms)
            key (bytes): The key for the record
            value (bytes): The value for the record
            headers (List[Tuple[str, bytes]]): The header fields for the record
            max_time_to_block_ms (int): The maximum time in milliseconds to
                block for buffer memory to be available

        Returns:
            tuple: (future, batch_is_full, new_batch_created)
        """
        assert isinstance(tp, TopicPartition), 'not TopicPartition'
        assert not self._closed, 'RecordAccumulator is closed'
        # We keep track of the number of appending thread to make sure we do
        # not miss batches in abortIncompleteBatches().
        self._appends_in_progress.increment()
        try:
            if tp not in self._tp_locks:
                with self._tp_locks[None]:
                    if tp not in self._tp_locks:
                        self._tp_locks[tp] = threading.Lock()

            with self._tp_locks[tp]:
                # check if we have an in-progress batch
                dq = self._batches[tp]
                if dq:
                    last = dq[-1]
                    future = last.try_append(timestamp_ms, key, value, headers)
                    if future is not None:
                        batch_is_full = len(dq) > 1 or last.records.is_full()
                        return future, batch_is_full, False

            size = max(self.config['batch_size'], estimated_size)
            log.debug("Allocating a new %d byte message buffer for %s", size, tp) # trace
            buf = self._free.allocate(size, max_time_to_block_ms)
            with self._tp_locks[tp]:
                # Need to check if producer is closed again after grabbing the
                # dequeue lock.
                assert not self._closed, 'RecordAccumulator is closed'

                if dq:
                    last = dq[-1]
                    future = last.try_append(timestamp_ms, key, value, headers)
                    if future is not None:
                        # Somebody else found us a batch, return the one we
                        # waited for! Hopefully this doesn't happen often...
                        self._free.deallocate(buf)
                        batch_is_full = len(dq) > 1 or last.records.is_full()
                        return future, batch_is_full, False

                records = MemoryRecordsBuilder(
                    self.config['message_version'],
                    self.config['compression_attrs'],
                    self.config['batch_size']
                )

                batch = ProducerBatch(tp, records, buf)
                future = batch.try_append(timestamp_ms, key, value, headers)
                if not future:
                    raise Exception()

                dq.append(batch)
                self._incomplete.add(batch)
                batch_is_full = len(dq) > 1 or batch.records.is_full()
                return future, batch_is_full, True
        finally:
            self._appends_in_progress.decrement()

```

​		我们看看sender这个类中有啥

![截屏2022-05-01 13.15.50](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-05-01%2013.15.50.jpg)

​		读读sender中的 run_once 方法。

```python
    def run_once(self):
        """Run a single iteration of sending."""
        while self._topics_to_add:
            self._client.add_topic(self._topics_to_add.pop())

        # get the list of partitions with data ready to send
        result = self._accumulator.ready(self._metadata)
        ready_nodes, next_ready_check_delay, unknown_leaders_exist = result

        # if there are any partitions whose leaders are not known yet, force
        # metadata update
        if unknown_leaders_exist:
            log.debug('Unknown leaders exist, requesting metadata update')
            self._metadata.request_update()

        # remove any nodes we aren't ready to send to
        not_ready_timeout = float('inf')
        for node in list(ready_nodes):
            if not self._client.is_ready(node):
                log.debug('Node %s not ready; delaying produce of accumulated batch', node)
                self._client.maybe_connect(node, wakeup=False)
                ready_nodes.remove(node)
                not_ready_timeout = min(not_ready_timeout,
                                        self._client.connection_delay(node))

        # create produce requests
        batches_by_node = self._accumulator.drain(
            self._metadata, ready_nodes, self.config['max_request_size'])

        if self.config['guarantee_message_order']:
            # Mute all the partitions drained
            for batch_list in six.itervalues(batches_by_node):
                for batch in batch_list:
                    self._accumulator.muted.add(batch.topic_partition)

        expired_batches = self._accumulator.abort_expired_batches(
            self.config['request_timeout_ms'], self._metadata)
        for expired_batch in expired_batches:
            self._sensors.record_errors(expired_batch.topic_partition.topic, expired_batch.record_count)

        self._sensors.update_produce_request_metrics(batches_by_node)
        requests = self._create_produce_requests(batches_by_node)
        # If we have any nodes that are ready to send + have sendable data,
        # poll with 0 timeout so this can immediately loop and try sending more
        # data. Otherwise, the timeout is determined by nodes that have
        # partitions with data that isn't yet sendable (e.g. lingering, backing
        # off). Note that this specifically does not include nodes with
        # sendable data that aren't ready to send since they would cause busy
        # looping.
        poll_timeout_ms = min(next_ready_check_delay * 1000, not_ready_timeout)
        if ready_nodes:
            log.debug("Nodes with data ready to send: %s", ready_nodes) # trace
            log.debug("Created %d produce requests: %s", len(requests), requests) # trace
            poll_timeout_ms = 0

        for node_id, request in six.iteritems(requests):
            batches = batches_by_node[node_id]
            log.debug('Sending Produce Request: %r', request)
            (self._client.send(node_id, request, wakeup=False)
                 .add_callback(
                     self._handle_produce_response, node_id, time.time(), batches)
                 .add_errback(
                     self._failed_produce, batches, node_id))

        # if some partitions are already ready to be sent, the select time
        # would be 0; otherwise if some partition already has some data
        # accumulated but not ready yet, the select time will be the time
        # difference between now and its linger expiry time; otherwise the
        # select time will be the time difference between now and the
        # metadata expiry time
        self._client.poll(timeout_ms=poll_timeout_ms)

```







​		









# 读java源码时要注意的点

## java动态绑定机制（重写方法时常用）

​		java多态的一个经典情形，aa对象有两个getresult() 方法！！！优先调用new后的 getresult()方法，并且会因为调用这个方法而使用这个对象。注意： 变量没有这个特点，这个动态绑定时针对方法而言的，变量在哪里声明就在哪里使用！！！

​		读java版本源码（真实源码，毕竟kafka时java写的）要注意。![截屏2022-04-29 17.25.37](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-29%2017.25.37.jpg)

​		优先调用了 BB内的 getresult 方法。BB内的 getresult 方法内使用了 i 这个变量，i 变量是在BB() 中声明过，变量在哪里声明哪里用。使用了 new BB() 对象的 i=20；20+20=40

​		结果是 40

![截屏2022-04-29 17.20.29](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-29%2017.20.29.jpg)

​		BB() 中的同名方法被注销，动态绑定依旧还在  ，但是aa 中只有了一个AA() 的 getresult() 方法，别无选择，动态绑定原则，AA() 的 getresult() 方法内调用的 i 变量是在AA() 内声明的，变量哪里声明哪里用。调用了 AA() 中的 i=10 ，10+10=20。

​		结果是20。

![截屏2022-04-29 17.52.04](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-29%2017.52.04.jpg)

​		以上情况，首先调用是 AA() 的 getresult() 方法，因为没得选只有它，然后在里面需要 geti() 方法，动态绑定原则 优先去 new BB() 里面去找，用到了BB内的 geti() 方法，geti() 方法用到了BB() 中声明的变量 i ，i=20 作为geti（）的返回值 传给 AA() 的 getresult() 方法，20+10=30。

​		结果是30。

​		以上的变量哪里声明哪里使用要灵活用，不是只能调用自己类内的变量，如果用了 super.i 那用的就是父类的 i 了，i 其实都是this.i 的简写，哪里声明哪里使用变量并不是固定的。

​		

## java方法重载

​		![截屏2022-04-29 18.10.35](kafka_API%E5%AE%9E%E6%88%98.assets/%E6%88%AA%E5%B1%8F2022-04-29%2018.10.35.jpg)

​		和 new BBB() 根本没关系，完全由aaa 前面声明的类型 来决定选哪一个test() 方法。

​		

