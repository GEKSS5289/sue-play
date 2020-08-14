# 🏀PlayFastDFS
> 系统环境:CentOS 7
## SpringBoot整合FastDFS
> 引入依赖
#### 🧊fastdfs-client
     <dependency>
                <groupId>com.github.tobato</groupId>
                <artifactId>fastdfs-client</artifactId>
                <version>1.26.7</version>
     </dependency>
> 配置application.yml
#### 🧊application.yml
    fdfs:
      connect-timeout: 30 #连接的超时时间
      so-timeout: 30 # 读取超时时间
      tracker-list: 192.168.182.150:22122 #track服务所在IP和端口号