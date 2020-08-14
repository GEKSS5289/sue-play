# 🏀PlayOSS
## SpringBoot整合OSS
> 引入依赖
#### 🧊aliyun-sdk-oss
       <dependency>
               <groupId>com.aliyun.oss</groupId>
               <artifactId>aliyun-sdk-oss</artifactId>
               <version>3.7.0</version>
       </dependency>
> 创建file.properties
#### 🧊file.properties
    file.endpoint=oss-cn-shenzhen.aliyuncs.com (外网访问地址)
    file.accessKeyId=【自己的KeyId】
    file.accessKeySecret=【自己的KeySecret】
    file.bucketName=shushun (bucketName bucket名字)
    file.objectName=sue-mall/images (类似文件存放的文件夹路径)
    file.ossHost=https://shushun.oss-cn-shenzhen.aliyuncs.com/ (外网访问域名)