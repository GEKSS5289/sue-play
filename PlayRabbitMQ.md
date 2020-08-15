# 🏀PlayRabbitMQ
### 📧分布式消息队列
> #### ✨SpringBoot2.x整合RabbitMq3.6.5
> ##### 🌠引入依赖
> ##### ⚙配置properties Or yml文件
# ✨SpringBoot2.x整合RabbitMq3.6.5
 
### 🌠引入依赖
    <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-amqp</artifactId>
    </dependency>
### ⚙配置properties Or yml文件
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