#### Eureka 服务商  


###  Eureka服务商示例

 
###  Eureka  server  

1.   程序
 
   ```java
    @SpringBootApplication
    @EnableEurekaServer
    public class MagicCloudApplication {
    
        public static void main(String[] args) {
            SpringApplication.run(MagicCloudApplication.class, args);
        }
    
    }

      
``` 
   ```yaml
    server:
      port: 9999
    
    eureka:
      instance:
        hostname: localhost
      client:
        registerWithEureka: false
        fetchRegistry: false
        serviceUrl:
          defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/

``` 


2.   @EnableEurekaServer 注解   
server.port  访问时 127.0.0.1：9999 或者 localhost:9999访问 配置中心serveer
  
   ```java
  @Import(EurekaServerMarkerConfiguration.class)
  public @interface EnableEurekaServer {
  }

  
    /**
     * {@link EurekaServerAutoConfiguration}.
     */
    @Configuration
    public class EurekaServerMarkerConfiguration {
    
        @Bean
        public Marker eurekaServerMarkerBean() {
            return new Marker();
        }
    
        class Marker {
    
        }
    
    }

  ``` 

3. EnableEurekaServer 注解
  只是  @Import(EurekaServerMarkerConfiguration.class) 了这个类
  查看  EurekaServerMarkerConfiguration 这个class，也没有什么
  注意看看注释  EurekaServerAutoConfiguration.java
![1](backend.png)
       
 

### Eureka  server 集群 配置

1. java代码同单机版

2. 配置
  - 新建三个配置文件  
     application-server1.yml
     application-server2.yml
     application-server3.yml
   - 修改host文件增加host   C:\Windows\System32\drivers\etc
      127.0.0.1  localhost server1 server2 server3
     
3. 文件
这三个文件格式 基本一样。有三个地方注意不同
 
   - server:   port: 9999   端口号
   - defaultZone : 不包含当前server的信息

application-server1.yml
  
   ```yaml
    server:
      port: 9998
    
    eureka:
      instance:
        hostname: server1
        instance-id: ${spring.application.name}:${server.port}
      client:
        registerWithEureka: false
        fetchRegistry: false
        serviceUrl:
          defaultZone: http://server2:9997/eureka/,http://server3:9996/eureka/
    
    spring:
      application:
        name: first
``` 
 
application-server2.yml
  
   ```yaml
   server:
     port: 9997
   
   
   eureka:
     instance:
       hostname: server2
       instance-id: ${spring.application.name}:${server.port}
     client:
       registerWithEureka: false
       fetchRegistry: false
       serviceUrl:
         defaultZone: http://server1:9998/eureka/,http://server3:9996/eureka/
   
   spring:
     application:
       name: second

``` 
 
  
application-server3.yml
  
   ```yaml
  server:
    port: 9996
  
  eureka:
    instance:
      hostname: server3
      instance-id: ${spring.application.name}:${server.port}
    client:
      registerWithEureka: false
      fetchRegistry: false
      serviceUrl:
        defaultZone: http://server1:9998/eureka/,http://server2:9997/eureka/
  
  spring:
    application:
      name: third

``` 

4. 结果如上图



### 注册一个服务代码如下

  ```java
        
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;
    
    @SpringBootApplication
    @EnableEurekaServer
    public class MagicCloudApplication {
    
        public static void main(String[] args) {
            SpringApplication.run(MagicCloudApplication.class, args);
        }
    
    }


      
``` 
### 查看服务

1. 访问 server1:9998

Instance Info
Name	Value
ipAddr	192.168.0.109
status	UP


![1](see_servcie.png)










