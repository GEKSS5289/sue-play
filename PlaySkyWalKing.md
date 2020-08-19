# 🏀PlaySkyWalKing
> ### 🍨SpringBoot应用搭载SkyWalking
> ### 🍥传统Tomcat应用搭载SkyWalking
> ### 🎨Java Agent插件
> ### 📄Java Agent配置详解
> ### 🎮Skywalking apm-customize-enhance-plugin使用教程
> ### 📢告警配置
> ### 🍢动态配置(Nacos)
> ### 🍻SkyWalKing+Nacos+Elasticsearch
# 🍨SpringBoot应用搭载SkyWalking
    启动时:
        java -javaagent:/opt/agent/skywalking-agent.jar -jar somr-spring-boot.jar
# 🍥传统Tomcat应用搭载SkyWalking
    linux t7-t9:
        修改tomcat/bin/catalina.sh第一行
            CATALINA_OPTS="$CATALINA_OPTS -javaagent:/opt/agent/skywalking-agent.jar"; export CATALINA_OPTS
    windows t7-t9:
        修改tomcat/bin/catalina.bat第一行
            set "CATALINA_OPTS=-javaagent:/opt/agent/skywalking-agent.jar"
# 🎨Java Agent插件
    Java Agent插件介绍
    Java Agent是插件化、可插拔的。Skywalking的插件分为三种：
    
    引导插件：在agent的 bootstrap-plugins 目录下
    内置插件：在agent的 plugins 目录下
    可选插件：在agent的 optional-plugins 目录下
    Java Agent只会启用 plugins 目录下的所有插件，bootstrap-plugins 目录以及 optional-plugins 目录下的插件不会启用。如需启用引导插件或可选插件，只需将JAR包移到 plugins 目录下，如需禁用某款插件，只需从 plugins 目录中移除即可。
    
## 🛤插件生态
### 引导插件
    目前只有两款引导插件：
    
    apm-jdk-http-plugin 用来是监测HttpURLConnection；
    apm-jdk-threading-plugin 用来监测Callable以及Runnable；
    有关引导插件的功能描述，可详见： https://github.com/apache/skywalking/blob/v6.6.0/docs/en/setup/service-agent/java-agent/README.md#bootstrap-class-plugins 。
    
### 内置插件
    内置插件主要用来为业界主流的技术与框架提供支持。所支持的技术&框架，详见 https://github.com/apache/skywalking/blob/v6.6.0/docs/en/setup/service-agent/java-agent/Supported-list.md 。
    
### 可选插件
    关于可选插件的功能描述，可详见 https://github.com/apache/skywalking/blob/v6.6.0/docs/en/setup/service-agent/java-agent/README.md 。
    
### 插件扩展
    Skywalking生态还有一些插件扩展，例如Oracle、Resin插件等。这部分插件主要是由于许可证不兼容/限制，Skywalking无法将这部分插件直接打包到Skywalking安装包内，于是托管在这个地址： https://github.com/SkyAPM/java-plugin-extensions ，使用方式：
    
    前往 https://github.com/SkyAPM/java-plugin-extensions/releases ，下载插件JAR包
    将JAR包挪到 plugins 目录即可启用。
# 📄Java Agent配置详解
    配置方式:
        系统属性(-D)
            java -javaagent:/opt/agent/skywalking-agent.jar -Dskywalking.agent.service_name=你想设置的值 -jar somr-spring-boot.jar
    代理选项:
        -javaagent:/path/to/skywalking-agent.jar=[option1]=[value1],[option2]=[value2]
        参考:java -javaagent:/opt/agent/skywalking-agent.jar=agent.service_name=你想设置的值 -jar somr-spring-boot.jar
    系统环境变量:
        agent.service_name=${SW_AGENT_NAME:Your_ApplicationName}
        这说明Skywalking会读取名为SW_AGENT_NAME的环境变量
    优先级:
        代理选项>系统属性(-D)>系统环境配置>配置文件
    参考文档: 
        https://github.com/apache/skywalking/blob/v6.6.0/docs/en/setup/service-agent/java-agent/README.md
# 🎮Skywalking apm-customize-enhance-plugin使用教程
    例子:
        public class TestService1 {
            public static void staticMethod(String str0, int count, Map m, List l, Object[] os) {
              // 业务逻辑
            }
          ...
        }
    那么想要对该方法监控，则如下操作:
        移动jar包:
            将 optional-plugins/apm-customize-enhance-plugin-6.5.0.jar 移动到 plugins 目录            
        编写增强规则:
            <?xml version="1.0" encoding="UTF-8"?>
            <enhanced>
                <class class_name="test.apache.skywalking.testcase.customize.service.TestService1">
                    <method method="staticMethod(java.lang.String,int.class,java.util.Map,java.util.List,[Ljava.lang.Object;)" operation_name="/is_static_method_args" static="true">
                        <operation_name_suffix>arg[0]</operation_name_suffix>
                        <operation_name_suffix>arg[1]</operation_name_suffix>
                        <operation_name_suffix>arg[3].[0]</operation_name_suffix>
                        <tag key="tag_1">arg[2].['k1']</tag>
                        <tag key="tag_2">arg[4].[1]</tag>
                        <log key="log_1">arg[4].[2]</log>
                    </method>
                </class>
            </enhanced>
## 配置说明
       class_name	                          要被增强的类
       method	                              类的拦截器方法
       operation_name	                      如果进行了配置，将用它替代默认的operation_name
       operation_name_suffix	              表示在operation_name后添加动态数据
       static	                              方法是否为静态方法
       tag	                                  将在local span中添加一个tag。key的值需要在XML节点上表示。
       log	                                  将在local span中添加一个log。key的值需要在XML节点上表示。
       arg[x]	                              表示输入的参数值。比如args[0]表示第一个参数。
       .[x]	                                  当正在被解析的对象是Array或List，你可以用这个表达式得到对应index上的对象。
       .[‘key’]	                              当正在被解析的对象是Map, 你可以用这个表达式得到map的key。
## 特别注意
        
        基本类型： 基本类型.class ，例如： int.class
        
        非基本类型： 类的完全限定名称 ，例如：java.lang.String
        
        数组：可以写个数组打印一下，就知道格式了，例如：
        
        public static void main(String[] args) {
                String[] s = new String[]{};
                System.out.println(s);
        
                int [] x = new int []{};
                System.out.println(x);
            }
        结果：
        
        [Ljava.lang.String;@1b0375b3
        [I@2f7c7260   
 ## 配置文件(agent.config)
        添加:
            plugin.customize.enhance_file=D:/apache-skywalking-apm-bin/agent/enhance.xml
 # 📢告警配置
       修改配置文件:
            cd /usr/local/apache-skywalking-apm-bin/config
            vim alarm-settings.yml
                rules:
                  endpoint_percent_rule:
                    metrics-name: endpoint_percent
                    include-names:
                      - dubbox-provider
                    exclude-names:
                      - dubbox-consumer
                    threshold: 75
                    op: <
                    period: 10
                    count: 3
                    silence-period: 10
                    message: Successful rate of endpoint {name} is lower than 75%
                webhooks(接受告警信息的服务地址):
                  - http://127.0.0.1/notify/
                  - http://127.0.0.1/go-wechat/
 ## 🎫规则
        规则定义
        规则的key的含义如下：
        
        endpoint_percent_rule：规则名称，将会在告警消息体中展示，必须唯一，且以 _rule 结尾；
        metrics-name：度量名称，取值可在 skywalking根目录/config/official_analysis.oal 中找到，填写其中的key即可，对OAL感兴趣的，可前往 https://github.com/apache/skywalking/blob/v6.6.0/docs/en/concepts-and-designs/oal.md 阅读其定义；
        include-names：将此规则作用于匹配的实体名称上，实体名称可以是服务名称或端点名称等
        exclude-names：将此规则作用于不匹配的实体名称上，实体名称可以是服务名称或端点名称等
        threshold：阈值
        op：操作符，目前支持 >、<、=
        period：多久检测一次告警规则，即检测规则是否满足的时间窗口，与后端开发环境匹配
        count：在一个period窗口中，如果实际值超过该数值将触发告警
        silence-period：触发告警后，在silence-period这个时间窗口中不告警，该值默认和period相同。例如，在时间T这个瞬间触发了某告警，那么在(T+10)这个时间段，不会再次触发相同告警
        message：告警消息体，{name} 会解析成规则名称
        默认规则
        Skywalking默认提供的 alarm-settings.yml ，定义的告警规则如下：
        
        过去3分钟内服务平均响应时间超过1秒
        服务成功率在过去2分钟内低于80%
        服务90%响应时间在过去3分钟内高于1000毫秒
        服务实例在过去2分钟内的平均响应时间超过1秒
        端点平均响应时间过去2分钟超过1秒
 ## Webhook
        Webhook表达的意思是，当告警发生时，将会请求的地址URL（用POST方法）。警报消息将会以 application/json 格式发送出去。消息例如：
        
        [{
        	"scopeId": 1, 
          "scope": "SERVICE",
          "name": "serviceA", 
        	"id0": 12,  
        	"id1": 0,  
          "ruleName": "service_resp_time_rule",
        	"alarmMessage": "alarmMessage xxxx",
        	"startTime": 1560524171000
        }, {
        	"scopeId": 1,
          "scope": "SERVICE",
          "name": "serviceB",
        	"id0": 23,
        	"id1": 0,
          "ruleName": "service_resp_time_rule",
        	"alarmMessage": "alarmMessage yyy",
        	"startTime": 1560524171000
        }]
        其中：
        
        scopeId、scope：作用域，取值详见 https://github.com/apache/skywalking/blob/v6.6.0/oap-server/server-core/src/main/java/org/apache/skywalking/oap/server/core/source/DefaultScopeDefine.java ；
        name：目标作用域下的实体名称；
        id0：作用域下实体的ID，与名称匹配；
        id1：暂不使用；
        ruleName： alarm-settings.yml 中配置的规则名称；
        alarmMessage：告警消息体；
        startTime：告警时间（毫秒），时间戳形式。
        根据如上消息体，可定义入参对象如下：
        
        public class SkyWalkingAlarm {
            private Integer scopeId;
            private String scope;
            private String name;
            private Integer id0;
            private Integer id1;
            private String ruleName;
            private String alarmMessage;
            private Long startTime;
            // getters and setters...
        }
        Controller编写如下即可：
        
        public class SkyWalkingAlarmController {
            @PostMapping("/alarm")
            public IMOOCJSONResult alarm(@RequestBody List<SkyWalkingAlarm> alarms) {
                // 接收到告警后的业务处理
                // 根据服务发现组件上面的服务名称，找到对应的/actuator/info
                // 进而找到对应的owner-email配置的值
                return IMOOCJSONResult.ok();
            }
        }
# 🍢动态配置(Nacos)
    从Skywalking 6.5.0开始，部分Skywalking配置项支持“动态配置”——这样修改完配置后，就无需重启Skywalking啦。
    
    支持动态配置的配置项如下：
    
    配置 Key	描述	值的格式
    receiver-trace.default.slowDBAccessThreshold	访问数据库慢的阈值，该值将会覆盖applciation.yml文件中的 receiver-trace/default/slowDBAccessThreshold 属性	例如：default:200,mongodb:50
    receiver-trace.default.uninstrumentedGateways	非仪表网关 相关配置，该值将会覆盖gateways.yml	格式同 gateways.yml
    alarm.default.alarm-settings	                告警 相关配置，该值将会覆盖 alarm-settings.yml.	格式同 alarm-settings.yml
    core.default.apdexThreshold	apdex               阈值 相关配置，该值将会覆盖service-apdex-threshold.yml	格式同 service-apdex-threshold.yml
    要想实现动态配置，需要一个额外的配置服务器。引入配置服务器之后，架构图如下：
    
    
    Skywalking支持使用如下配置服务器：
    
    Dynamic Configuration Service
    Apollo
    Nacos
    Zookeeper
    Etcd
    Consul
    就目前来看，除 Dynamic Configuration Service 尚不完备以外，其他的都可以直接用在生产。
 ## 安装Nacos
 ## 修改Skywalking配置
        修改Skywalking的application.yml
            找到:
                configuration:
                  none:
                注释掉 none这一行，即改为：
                # none
            解开Nacos相关配置
                configuration:
                  nacos:
                    # Nacos Server IP
                    serverAddr: 127.0.0.1
                    # Nacos Server端口
                    port: 8848
                    # Nacos Group
                    group: 'skywalking'
                    # Nacos namespace
                    namespace: ''
                    # 多久从Nacos Server上同步一次配置，单位秒
                    period : 60
                    # 集群名称
                    clusterName: "default"
## 以管理告警规则为例，在Nacos Server上创建DataId为 alarm.default.alarm-settings （其他配置类似，参照本文最上面的表格即可），配置的值参照 alarm-settings.yml 的写法。例如：
       rules:
         service_resp_time_rule:
           metrics-name: service_resp_time
           op: ">"
           threshold: 1
           period: 2
           count: 1
           silence-period: 5
           message: Response time of service {name} is more than 1ms in 1 minutes of last 2 minutes.
# 🍻SkyWalKing+Nacos+Elasticsearch
    搭建Nacos集群
    参考Nacos搭建文档
    
    搭建Elasticsearch集群
    详见 ElasticSearch-6.6.0之集群环境搭建
    
    搭建Skywalking集群
    准备工作完成后，下面来搭建一个2实例的Skywalking集群。由于我只有1台服务器，所以就把两个Skywalking实例搭建在一起了。实际项目中应当将不同Skywalking实例部署在不同服务器。
    
    主机规划
    实例	IP	占用端口
    Skywalking 实例1	127.0.0.1	11800(gRPC)、12800(REST)、8080(UI)
    Skywalking 实例2	127.0.0.1	11801(gRPC)、12801(REST)
    TIPS
    
    这里，我们只搭建了一个UI，实际项目也可搭建多个UI，然后在UI前面再挂个NGINX之类的反向代理工具。
    
    开始搭建
    解压
    将 apache-skywalking-apm-6.6.0.tar 复制两份，并解压。
    backend相关配置
    修改 config/application.yml ，找到类似如下的段落，按需修改IP和端口。
    
    core:
      default:
        # Mixed: Receive agent data, Level 1 aggregate, Level 2 aggregate
        # Receiver: Receive agent data, Level 1 aggregate
        # Aggregator: Level 2 aggregate
        role: ${SW_CORE_ROLE:Mixed} # Mixed/Receiver/Aggregator
        restHost: ${SW_CORE_REST_HOST:0.0.0.0}
        restPort: ${SW_CORE_REST_PORT:12800}
        restContextPath: ${SW_CORE_REST_CONTEXT_PATH:/}
        gRPCHost: ${SW_CORE_GRPC_HOST:0.0.0.0}
        gRPCPort: ${SW_CORE_GRPC_PORT:11800}
    注：gRPCHost、gRPCPort是agent发送数据的地址，restHost、restPort是UI请求的地址
    
    找到类似如下的段落，按需配置Nacos相关信息。
    
    cluster:
      # 注意，务必注释掉standalone这一行。默认情况下用的单机模式(standalone)，现在要改成集群模式，所以得注释掉。否则Skywalking将无法启动！
      # standalone:
      nacos:
        # Skywalking在Nacos Server的服务名称
        serviceName: ${SW_SERVICE_NAME:"SkyWalking_OAP_Cluster"}
        # Nacos Server地址用http://ip:端口的形式
        hostPort: ${SW_CLUSTER_NACOS_HOST_PORT:localhost:8848}
        # Nacos的namespace
        namespace: 'public'
    找到类似如下的段落，按需配置Elasticsearch相关信息，一般配置clusterNodes即可。
    
    storage:
      elasticsearch:
        # Elasticsearch的namespace
        nameSpace: ""
        # Elasticsearch集群的Node列表，多个用,分隔例如：localhost:9200,localhost:9201
        clusterNodes: localhost:9200
        # 和Elasticsearch集群通信的协议
        # protocol: ${SW_STORAGE_ES_HTTP_PROTOCOL:"http"}
        # 证书
        # trustStorePath: ${SW_SW_STORAGE_ES_SSL_JKS_PATH:"../es_keystore.jks"}
        # trustStorePass: ${SW_SW_STORAGE_ES_SSL_JKS_PASS:""}
    
        # user: ${SW_ES_USER:""}
        # password: ${SW_ES_PASSWORD:""}
        # indexShardsNumber: ${SW_STORAGE_ES_INDEX_SHARDS_NUMBER:2}
        # indexReplicasNumber: ${SW_STORAGE_ES_INDEX_REPLICAS_NUMBER:0}
        # Those data TTL settings will override the same settings in core module.
        # recordDataTTL: ${SW_STORAGE_ES_RECORD_DATA_TTL:7} # Unit is day
        # otherMetricsDataTTL: ${SW_STORAGE_ES_OTHER_METRIC_DATA_TTL:45} # Unit is day
        # monthMetricsDataTTL: ${SW_STORAGE_ES_MONTH_METRIC_DATA_TTL:18} # Unit is month
        # Batch process setting, refer to https://www.elastic.co/guide/en/elasticsearch/client/java-api/5.5/java-docs-bulk-processor.html
        # bulkActions: ${SW_STORAGE_ES_BULK_ACTIONS:1000} # Execute the bulk every 1000 requests
        # flushInterval: ${SW_STORAGE_ES_FLUSH_INTERVAL:10} # flush the bulk every 10 seconds whatever the number of requests
        # concurrentRequests: ${SW_STORAGE_ES_CONCURRENT_REQUESTS:2} # the number of concurrent requests
        # resultWindowMaxSize: ${SW_STORAGE_ES_QUERY_MAX_WINDOW_SIZE:10000}
        # metadataQueryMaxSize: ${SW_STORAGE_ES_QUERY_MAX_SIZE:5000}
        # segmentQueryMaxSize: ${SW_STORAGE_ES_QUERY_SEGMENT_SIZE:200}
    注释掉如下段落。这是因为默认情况Skywalking将数据存储在H2，但现在已经用Elasticsearch存储数据了，所以不需要用H2了。
    
      h2:
        driver: ${SW_STORAGE_H2_DRIVER:org.h2.jdbcx.JdbcDataSource}
        url: ${SW_STORAGE_H2_URL:jdbc:h2:mem:skywalking-oap-db}
        user: ${SW_STORAGE_H2_USER:sa}
        metadataQueryMaxSize: ${SW_STORAGE_H2_QUERY_MAX_SIZE:5000}
    UI相关配置
    修改 webapp/webapp.yml ，找到类似如下段落，修改listofServers。
    
    server:
      port: 8080
    collector:
      path: /graphql
      ribbon:
        ReadTimeout: 10000
        # Point to all backend's restHost:restPort, split by ,
        listOfServers: 127.0.0.1:12800,127.0.0.1:12801
    启动
    启动后端(2个实例)
    sh bin/oapService.sh
    启动UI(1个实例)
    sh bin/webappService.sh
    agent使用
    修改 agent/config/agent.config ，将 collector.backend_service 修改为 127.0.0.1:11800,127.0.0.1:11801 。
    启动应用 java -javaagent:/xxxx/skywalking-agent.jar -jar xxxx.jar 。
    效果
    Nacos Server上可以看到两个Skywalking实例：
       