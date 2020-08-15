# 🏀PlayRabbitMQ
### 📧分布式消息队列
> #### ✨SpringBoot2.x整合RabbitMq3.6.5
> ##### 🌠引入依赖(producer,consumer都需要引入)
> ##### ⚙配置properties Or yml文件(producer)
> ##### ⚙配置properties Or yml文件(consumer)
> ##### 🧱producer端代码示例
> ##### 💳consumer端代码示例
> ##### 🛀MQproducer关注的可靠性问题
> ##### 🛀MQconsumer关注的可靠性问题
# ✨SpringBoot2.x整合RabbitMq3.6.5
 
### 🌠引入依赖(producer,consumer都需要引入)
    <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-amqp</artifactId>
    </dependency>
### ⚙配置properties Or yml文件(producer)
    server.port=8001
    
    spring.application.name=sue-rabbitmq-producer
    
    
    #rabbitmq服务地址
    spring.rabbitmq.addresses=192.168.182.150:5672,192.168.182.151:5672,192.168.182.152:5672
    spring.rabbitmq.username=guest
    spring.rabbitmq.password=guest
    #虚拟机
    spring.rabbitmq.virtual-host=/
    #连接超时时间
    spring.rabbitmq.connection-timeout=15000
    
    #是否启用消息确认模式
    spring.rabbitmq.publisher-confirms=true
    #设置return消息模式，注意要和mandatoryp一起配合使用
    #spring.rabbitmq.publisher-returns=true
    #spring.rabbitmq.template.mandatory=true
### ⚙配置properties Or yml文件(consumer)
    server.port=8002
    
    spring.application.name=sue-rabbitmq-consumer
    
    
    #rabbitmq服务地址
    spring.rabbitmq.addresses=192.168.182.150:5672,192.168.182.151:5672,192.168.182.152:5672
    spring.rabbitmq.username=guest
    spring.rabbitmq.password=guest
    #虚拟机
    spring.rabbitmq.virtual-host=/
    #连接超时时间
    spring.rabbitmq.connection-timeout=15000
    
    ## 表示消费者消费成功消息以后需要手工的进行ack 默认为
    spring.rabbitmq.listener.simple.acknowledge-mode=manual
    spring.rabbitmq.listener.simple.concurrency=5
    spring.rabbitmq.listener.simple.max-concurrency=10
    spring.rabbitmq.listener.simple.prefetch=1
### 🧱producer端代码示例
    @Component
    public class RabbitSender {
    
    
        @Autowired
        private RabbitTemplate rabbitTemplate;
    
    
        /**
         * 这里就是确认消息回调监听接口，用于确认消息是否被broker所收到
         */
        final RabbitTemplate.ConfirmCallback confirmCallback = new RabbitTemplate.ConfirmCallback(){
            /**
             *
             * @param correlationData 作为一个唯一的标识
             * @param ack broker是否落盘成功 true:成功 false:失败
             * @param cause 失败的异常信息
             */
            @Override
            public void confirm(CorrelationData correlationData, boolean ack, String cause) {
                System.out.println(ack);
            }
        };
    
        /**
         *  对外发送的消息方法
         * @param message 具体的消息内容
         * @param properties 额外的附加属性
         * @throws Exception
         */
        public void send(Object message, Map<String,Object> properties) throws Exception{
            MessageHeaders mhs = new MessageHeaders(properties);
            Message<?> msg = MessageBuilder.createMessage(message, mhs);
            CorrelationData correlationData = new CorrelationData(UUID.randomUUID().toString());
            rabbitTemplate.setConfirmCallback(confirmCallback);
            rabbitTemplate.convertAndSend(
                    "exchange-1",
                    "springboot.rabbit",
                    msg,
                    message1 -> {
                        System.out.println(message1);
                        return message1;
                    },
                    correlationData);
    
    
        }
    }
### 💳consumer端代码示例
    @Component
    public class RabbitReceive {
    
        /**
         * @RabbitListener @QueueBinding @Queue @Exchange
         * @param message
         * @param channel
         * @throws Exception
         */
        @RabbitListener(bindings = @QueueBinding(
                value = @Queue(value = "queue-1",durable = "true"),
                exchange = @Exchange(
                        name = "exchange-1",
                        durable = "true",
                        type="topic",
                        ignoreDeclarationExceptions = "true"
                ),
                key = "springboot.*"
        ))
        @RabbitHandler
        public void onMessage(Message message, Channel channel) throws Exception{
            //消费消息
            System.out.println("----------------");
            System.out.println("消费消息:"+message.getPayload());
    
            //处理成功后 获取deliverTag 并进行手工ACK操作，因为我们配置文件里配置的是手工签收
            Long deliverTag = (Long)message.getHeaders().get(AmqpHeaders.DELIVERY_TAG);
            channel.basicAck(deliverTag,false);
        }
    }
### 🛀MQproducer关注的可靠性问题
  > #### 关注的可靠性问题:消息可靠性投递问题(生产者保证消息可靠投递)

### 🛀MQConsumer关注的可靠性问题
  > #### 关注的可靠性问题:消费消息幂等性问题(消费者保证消息可靠消费)  