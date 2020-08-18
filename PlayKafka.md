# 🏀PlayKafka
### 📧分布式消息队列
> #### 🚀添加依赖与配置打包方式
> #### ⚒创建生产者
> #### 🌽创建消费者者
> #### 🎉SpringBoot整合Kafka
# 🚀添加依赖
    <dependency>
           <groupId>org.apache.kafka</groupId>
           <artifactId>kafka_2.12</artifactId>
    </dependency>
    配置打包方式
                <build>
                    <finalName>sue-kafka</finalName>
                    <!-- 打包时包含properties、xml -->
                    <resources>
                        <resource>
                            <directory>src/main/java</directory>
                            <includes>
                                <include>**/*.properties</include>
                                <include>**/*.xml</include>
                            </includes>
                            <!-- 是否替换资源中的属性-->
                            <filtering>true</filtering>
                        </resource>
                        <resource>
                            <directory>src/main/resources</directory>
                        </resource>
                    </resources>
                    <plugins>
                        <plugin>
                            <groupId>org.springframework.boot</groupId>
                            <artifactId>spring-boot-maven-plugin</artifactId>
                            <configuration>
                                <mainClass>com.sue.kafka.api.Application</mainClass>
                            </configuration>
                        </plugin>
                    </plugins>
                </build>
# ⚒创建生产者
    public class QuickStartProducer {
        public static void main(String[] args){
            //配置生产者启动关键数据
            Properties properties = new Properties();
            //BOOTSTRAP_SERVERS_CONFIG:连接kafka集群服务列表，如果有多个用逗号分隔
            properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG,"192.168.182.150:9092");
            //CLIENT_ID_CONFIG:这个属性标记kafkaclient的ID
            properties.put(ProducerConfig.CLIENT_ID_CONFIG,"quickstart-producer");
            //对kafka的key以及value做序列化
            //key:是kafka用于做消息投递计算具体投递到对应的主题的哪一个partition而需要
            //value:实际发送的内容
            //为什么需要序列化? 因为我们kafkaBroker在接收消息的时候必须要以二进制的方式接收，所以需要序列化
            properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
            properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG,StringSerializer.class.getName());
            //创建kafkaProducer对象，传递properties属性参数集合
            KafkaProducer<String,String> producer = new KafkaProducer<String, String>(properties);
            //构造消息内容
            User user = new User("001","张三");
            ProducerRecord<String,String> record = new ProducerRecord<>("test-quickstart", JSON.toJSONString(user));
            //发送消息
            producer.send(record);
            //关闭生产者
            producer.close();
        }
    }
# 🌽创建消费者者
    public class QuickStartConsumer {
        public static void main(String[] args){
            //配置属性参数
            Properties properties = new Properties();
            properties.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG,"192.168.182.150:9092");
            properties.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
            properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
    
            //非常重要的属性配置 与我们消费者订阅组有关系
            properties.put(ConsumerConfig.GROUP_ID_CONFIG,"quickstart-group");
            //常规属性
            properties.put(ConsumerConfig.SESSION_TIMEOUT_MS_CONFIG,10000);
            //消费者提交offset：自动提交 & 手动提交 默认是自动提交
            properties.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG,true);
            properties.put(ConsumerConfig.AUTO_COMMIT_INTERVAL_MS_CONFIG,5000);
    
            //创建消费者对象
            KafkaConsumer<String,String> consumer = new KafkaConsumer<>(properties);
            //订阅你感兴趣的主题
            consumer.subscribe(Collections.singletonList(Const.TOPIC_QUICKSTART));
            System.err.println("quickstart consumer started...");
            try {
                //采用拉取的方式消费数据
                while(true){
                    //等待多久拉取一次消息
                    //拉取TOPIC_QUICKSTART主题里面的所有消息
                    //topic 和 partition是一对多关系 一个topic可以用多个partition
                    ConsumerRecords<String, String> poll = consumer.poll(Duration.ofMillis(1000));
                    //因为partition中存储的，所以需要遍历partition集合
                    poll.partitions().forEach(topicPartition -> {
    
                        //通过TopicPartition获取到实际records对象中数据集合
                        List<ConsumerRecord<String, String>> records = poll.records(topicPartition);
                        //获取当前topicPartition消息条数
                        int size = records.size();
                        //获取到TopicPartition对应的主题名称
                        String topic = topicPartition.topic();
                        System.out.println(String.format("---获取topic:%s,分区位置:%s,消息总数:%s",
                                topic,
                                topicPartition.partition(),
                                size
                        ));
    
    
                        for(int i = 0;i<size;i++){
                            //实际数据
                            ConsumerRecord<String, String> consumerRecord = records.get(i);
                            String value = consumerRecord.value();
                            long offset = consumerRecord.offset();
                            long commitOffset = offset+1;
                            System.out.println(String.format("获取实际消息value:%s,消息offset:%s,提交offset:%s",value,offset,commitOffset));
                        }
                    });
                }
            }finally {
                consumer.close();
            }
    
    
        }
    }
# 🎉SpringBoot整合Kafka
> ### ⛑Prodcuer端整合
> ### 👒Consumer端整合  
> ### 🔬发送消息测试      
## ⛑Prodcuer端整合
 ###引入依赖:
          <dependency>
                    <groupId>org.springframework.kafka</groupId>
                    <artifactId>spring-kafka</artifactId>
          </dependency>
 ### 配置文件:
          server.port=8001
          ##springBoot整合kafka
          spring.kafka.bootstrap-servers=192.168.182.150:9092
          ##kafka producer发送消息失败时的一个重试次数
          spring.kafka.producer.retries=0
          ##批量发送数据的配置
          spring.kafka.producer.batch-size=16384
          spring.kafka.producer.buffer-memory=33554432
          ##kafka消息序列化
          spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
          spring.kafka.producer.value-serializer=org.apache.kafka.common.serialization.StringSerializer
          ##acks=0 生产者在成功写入消息之前不会等待任何来自服务器的响应
          ##acks=1 只要集群的首领节点收到消息，生产者就会收到来自服务器的成功响应
          ##acks=-1 表示分区leader必须等待消息被成功写入所有的ISR副本（同步副本）中才认为producer请求成功，这种方案提供做高消息持久性保证，但是理论上吞吐量是最差的
          ##实际工作中的配置
          spring.kafka.producer.acks=1 
  ### ProducerService
          @Component
          @Slf4j
          public class KafkaproducerService {
              @Resource
              private KafkaTemplate<String,Object> kafkaTemplate;
          
              public void sendMessage(String topic,Object object){
          
                  ListenableFuture<SendResult<String, Object>> future = kafkaTemplate.send(topic, object);
                  future.addCallback(new ListenableFutureCallback<SendResult<String, Object>>() {
          
                      public void onFailure(Throwable throwable) {
                          log.error("发送消息失败:"+throwable.getMessage());
                      }
          
                      public void onSuccess(SendResult<String, Object> stringObjectSendResult) {
                          log.info("发送消息成功:"+stringObjectSendResult.toString());
                      }
                  });
          
              }
          }

## 👒Consumer端整合  
 ### 引入依赖:
          <dependency>
                    <groupId>org.springframework.kafka</groupId>
                    <artifactId>spring-kafka</artifactId>
          </dependency>
 ### 配置文件
          server.port=8002
          ##springBoot整合kafka
          spring.kafka.bootstrap-servers=192.168.182.150:9092
          ##consumer消息的签收机制:手工签收
          spring.kafka.consumer.enable-auto-commit=false
          spring.kafka.listener.ack-mode=manual
          ## earliest 当各分区下有已提交的offset时，从提交的offset开始消费；无提交的offset时，从头开始消费
          ## latest 当各分区下有已提交的offset时，从提交的offset开始消费；无提交的offset时，消费新产生的该分区下的数据
          ## none topic各分区都存在已提交的offset时，从offset后开始消费；只要有一个分区不存在已提交的offset，则抛出异常
          spring.kafka.consumer.auto-offset-reset=earliest
          spring.kafka.consumer.key-deserializer=org.apache.kafka.common.serialization.StringDeserializer
          spring.kafka.consumer.value-deserializer=org.apache.kafka.common.serialization.StringDeserializer
          spring.kafka.listener.concurrency=5
  ### ConsuemrService
          @Component
          @Slf4j
          public class KafkaConsumerService {
              @KafkaListener(groupId = "group02",topics = "topic02")
              public void onMessage(
                      ConsumerRecord<String,Object>record,
                      Acknowledgment acknowledgment,
                      Consumer<?,?> consumer){
                  log.info("消费端接收消息:{}",record.value());
                  //手工签收
                  acknowledgment.acknowledge();
              }
          }

## 🔬发送消息测试
          @RunWith(SpringRunner.class)
          @SpringBootTest
          public class Application {
              @Autowired
              private KafkaproducerService kafkaproducerService;
              @Test
              public void send(){
                  String topic = "topic02";
                  for(int i = 0;i<10;i++){
                      kafkaproducerService.sendMessage(topic,"hello"+i);
                  }
          
              }
          }
## 👌查看消费进度
          ./kafka-consumer-groups.sh --bootstrap-server 192.168.182.150:9092 --describe --group group02

          