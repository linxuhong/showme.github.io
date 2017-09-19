                           sina motan 分析  多环境打包
## 1. 场景
- motan是微博开源的一套轻量级、方便使用的RPC框架
- 项目地址：https://github.com/weibocom/motan
- Motan 是基于 Java 的高性能轻量级 RPC 框架，
   - 其具备实用的服务治理功能和 RPC 协议扩展能力。
   - 服务发现灵活支持多种配置管理组件，基于高并发高负载场景的高可用策略优化，
   - 良好的 SPI(Service Provider Interface)扩展，详细的调用统计，灵活支持多种 RPC 传输协议，在使用上，
   - 无缝支持Spring 配置方式，通过简单灵活的配置即可快速接入使用

# 2 架构图
+ ![RPC](/http://n.sinaimg.cn/tech/transform/20160510/JtXy-fxryhhu2382987.jpg)
+ ![Motan](/hhttp://n.sinaimg.cn/tech/transform/20160510/V2ql-fxryhhh1870084.jpg)

# 3 RPC框架要重点核心点 
## 推荐[从motan看RPC框架实现](http://kriszhang.com/motan-rpc-impl/)
## 核心内容  
   1. 如何与Spring做集成？
   2. SPI机制的实现原理是什么？为什么要用这个？
   3. 这么多的配置，究竟是如何在类间传递和保存的？
   4. 服务降级是如何做的？
   5. motan协议究竟是什么样子的？
   6. 服务发现和注册究竟是怎么做的？或者说客户端是如何找到服务端的？
   7. 为什么要用动态代理，到底proxy了什么？
   8. cluster的负载均衡和ha究竟是如何实现的？
   9. transport层是怎么进行抽象的？
   10.  client对server的心跳检测是怎么实现的呢？
   11. client端的连接池是如何管理的？
   12. server端的连接是如何共享的？
 
## 源代码分析关注点下几点
    1. SPI机制的实现原理是什么？为什么要用这个？
    2. 这么多的配置，究竟是如何在类间传递和保存的？
    3. motan协议究竟是什么样子的？
    4. 为什么要用动态代理，到底proxy了什么？
	5. transport层是怎么进行抽象的？							 
	6. transport层是怎么进行抽象的？							 
    7.  client对server的心跳检测是怎么实现的呢？
    8. client端的连接池是如何管理的？
    9. server端的连接是如何共享的？
    
>  我将从以上几点对motan的源代码进行分析debug
   由于spring集成与解决涉及的内容比较多与杂，暂时先不作分析 
   

##  RPC本质的理解
1. 对于软件工程师来讲，形如object.method()的方法调用实在是太过熟悉，
当我们在同一个JVM进程内执行方法调用的时候，一切都显得顺其自然。
然而如果我们将上述的调用过程拆分成两个部分—-方法的调用端和方法的实现端，
然后将他们分别放置到不同的进程中，使得调用端和实现端能够做到跨操作系统，跨网络。这便是RPC的本质。

2. 从功能角度来讲，RPC框架可以分为服务治理型和多语言型。motan显然属于前者，
因此对motan框架可以简单的理解为：分离方法的调用和实现，并具双端服务治理功能。

> 1. 在同一jvm中就是 method.invoke(),而跨vm与网络时，则涉及到对象与数据的交换，而client方只用到了一个接口而已。
> 2. 如果打电话，通过电话号找到远程的你call you ,而你我分属不同的vm进程
 

# Quick Start       
## 入门例子
1. server demo 服务
   ```java
    import com.weibo.api.motan.common.MotanConstants;.
    import ...
    public class MotanApiExportDemo {
        public static void main(String[] args) throws InterruptedException {
            ServiceConfig<MotanDemoService> motanDemoService = new ServiceConfig<MotanDemoService>();
    
            // 设置接口及实现类
            motanDemoService.setInterface(MotanDemoService.class);
            motanDemoService.setRef(new MotanDemoServiceImpl());
    
            // 配置服务的group以及版本号
            motanDemoService.setGroup("motan-demo-rpc");
            motanDemoService.setVersion("1.0");
    
            // 配置注册中心直连调用
            // RegistryConfig directRegistry = new RegistryConfig();
            // directRegistry.setRegProtocol("local");
            // directRegistry.setCheck("false"); //不检查是否注册成功
            // motanDemoService.setRegistry(directRegistry);
    
            // 同志我本机没有安装 zk,因此将zk的地上注释 ，此时motan将默认使用local来注册
            RegistryConfig zookeeperRegistry = new RegistryConfig();
            // 配置ZooKeeper注册中心
           //   zookeeperRegistry.setRegProtocol("zookeeper");
           //   zookeeperRegistry.setAddress("127.0.0.1:2181");
            motanDemoService.setRegistry(zookeeperRegistry);
    
            // 配置RPC协议
            ProtocolConfig protocol = new ProtocolConfig();
            protocol.setId("motan");
            protocol.setName("motan");
            motanDemoService.setProtocol(protocol);
    
            motanDemoService.setExport("motan:8002");
            motanDemoService.export();
    
            MotanSwitcherUtil.setSwitcherValue(MotanConstants.REGISTRY_HEARTBEAT_SWITCHER, true);
    
            System.out.println("server start...");
        }
    }
     ```
     
-  ServiceConfig 所以显露外部服务的公用类，如果是用spring集成，将会使用其子类 ServiceConfigBean，暂时不讨论
   1. ServiceConfig 有两个属性  interface ,ref 分别代码接口与实现类。然后指名服务 的分组与版本.
   2. RegistryConfig  代码服务生成后，注册地上，暂时不用zk，因此注释设置相关属性的地方。
   3. ProtocolConfig 服务是用保种协议进行传输与调用的 motan
   4. ServiceConfig 设置~ setRegistryConfig,setProtocolConfig~ ,然后调用export就实现了

2. client调用
   ```java
         
    public class MotanApiClientDemo {
    
        public static void main(String[] args) throws InterruptedException {
            RefererConfig<MotanDemoService> motanDemoServiceReferer = new RefererConfig<MotanDemoService>();
    
            // 设置接口及实现类
            motanDemoServiceReferer.setInterface(MotanDemoService.class);
    
            // 配置服务的group以及版本号
            motanDemoServiceReferer.setGroup("motan-demo-rpc");
            motanDemoServiceReferer.setVersion("1.0");
            motanDemoServiceReferer.setRequestTimeout(300);
    
            // 配置注册中心直连调用
            // RegistryConfig directRegistry = new RegistryConfig();
            // directRegistry.setRegProtocol("local");
            // motanDemoServiceReferer.setRegistry(directRegistry);
    
            // 配置ZooKeeper注册中心
            RegistryConfig zookeeperRegistry = new RegistryConfig();
    //        zookeeperRegistry.setRegProtocol("zookeeper");
    //        zookeeperRegistry.setAddress("127.0.0.1:2181");
            motanDemoServiceReferer.setRegistry(zookeeperRegistry);
    
            // 配置RPC协议
            ProtocolConfig protocol = new ProtocolConfig();
            protocol.setId("motan");
            protocol.setName("motan");
            motanDemoServiceReferer.setProtocol(protocol);
            motanDemoServiceReferer.setDirectUrl("localhost:8002");  // 注册中心直连调用需添加此配置
    
            // 使用服务
            MotanDemoService service = motanDemoServiceReferer.getRef();
            for (int i=0;i<1000;i++) {
                System.out.println(service.hello("motan " +i));
                Thread.sleep(1000);
            }
    
            System.exit(0);
        }
    }

   ```
   
 -  RefererConfig 客户端对motan服务的一个封装，如果是用spring集成，将会使用其子类 RefererConfigBean，暂时不讨论
    1. RefererConfig 有两个属性  interface ,ref 分别代码接口与实现类。然后指名服务 的分组与版本.
    2. RegistryConfig  代码服务生成后，注册地上，暂时不用zk，因此注释设置相关属性的地方。
    3. ProtocolConfig 服务是用保种协议进行传输与调用的 motan
    4. RefererConfig#gerRef().hello() 通过远程调用返回 hello  motan
    > 这里使用setDirectUrl localhost:8002 主要是因为client,server在同一台机器上，但是端口仍然监听 
    
                        
***
     
##  公共组件部分
1. SPI：Service Provider Interface，主要通过ExtensionLoader提供扩展点功能，用来动态装载接口具体实现，以提供客户端扩展能力。
2. Logger：使用slf4j，提供整个框架统一的日志记录工具。
3. Statistic：使用定时回调方式，收集和记录框架监控信息。
4. URL：非Jdk里的URL。他对协议，路径，参数的统一抽象，系统也是使用URL来保存和读取配置信息的。
5. Switcher：提供开关控制服务，能够控制框架关键路径的升级和降级。
6. Exception：统一异常处理，分为业务异常，框架异常，服务异常等。
***
## 层的作用：

1. Config层：主要提供了配置读取，解析，实体生成。同时他也是整个框架的入口，
2. Proxy层：服务端无proxy，客户端具有代理功能，他通过InvocationHandler来拦截方法调用。目前只使用了jdk原生动态代理工具。
3. Registry层：用来进行服务发现和注册，server端进行服务的注册，client进行服务发现。目前有zk,consul的实现，还有对directUrl（就是p2p，不借助中心）的特殊处理。
4. Cluster层：集群功能，他提供了集群的服务暴露、服务引用、负载均衡，HA等。
5. Protocol层：协议层，目前只支持injvm和motan协议，主要就是提供给下层协议编码和解析，并且使用filter/pipe模式进行访问日志，最大并发量控制等功能。
6. Transport层：主要处理网络连接，他主要抽象了Client和Server接口。使用netty框架。

 
 ***
##  Server端
### 要关注的问题
1. 配置解析与传递
2。服务URL与注册URL
3. SPI 机制
4. transport 封装
5. 服务暴露
6. 类图

***

### 从ServiceConfig.export()开始。
#### 类图
  ![ServiceConfig](ServiceConfig.png)
1. ServiceConfig主要逻辑：
```java

  
public class ServiceConfig<T> extends AbstractServiceConfig {
    private static ConcurrentHashSet<String> existingServices = new ConcurrentHashSet<String>();
    // 具体到方法的配置
    protected List<MethodConfig> methods;
    // 接口实现类引用
    private T ref;

    // service 对应的exporters，用于管理service服务的生命周期
    private List<Exporter<T>> exporters = new CopyOnWriteArrayList<Exporter<T>>();
    private Class<T> interfaceClass;
    private BasicServiceInterfaceConfig basicService;
    private AtomicBoolean exported = new AtomicBoolean(false);
    // service的用于注册的url，用于管理service注册的生命周期，url为regitry url，内部嵌套service url。
    private ConcurrentHashSet<URL> registereUrls = new ConcurrentHashSet<URL>();

    protected boolean serviceExists(URL url) {
        return existingServices.contains(url.getIdentity());
    }

    public synchronized void export() {
        if (exported.get()) {
            return;
        }

        checkInterfaceAndMethods(interfaceClass, methods);

        //  AbstractInterfaceConfig#loadRegistryUrls  父类的方法来收集
        List<URL> registryUrls = loadRegistryUrls();
        if (registryUrls == null || registryUrls.size() == 0) {
            throw new IllegalStateException("Should set registry config for service:" + interfaceClass.getName());
        }

        Map<String, Integer> protocolPorts = getProtocolAndPort();
        for (ProtocolConfig protocolConfig : protocols) {
            Integer port = protocolPorts.get(protocolConfig.getId());
            if (port == null) {
                throw new MotanServiceException();
            }
            doExport(protocolConfig, port, registryUrls);
        }
        afterExport();
    }

    @SuppressWarnings("unchecked")
    private void doExport(ProtocolConfig protocolConfig, int port, List<URL> registryURLs) {
        String protocolName = protocolConfig.getName();
        if (protocolName == null || protocolName.length() == 0) {
            protocolName = URLParamType.protocol.getValue();
        }

        String hostAddress = host;
        if (StringUtils.isBlank(hostAddress) && basicService != null) {
            hostAddress = basicService.getHost();
        }
        if (NetUtils.isInvalidLocalHost(hostAddress)) {
            hostAddress = getLocalHostAddress(registryURLs);
        }

        Map<String, String> map = new HashMap<String, String>();

        map.put(URLParamType.nodeType.getName(), MotanConstants.NODE_TYPE_SERVICE);
        map.put(URLParamType.refreshTimestamp.getName(), String.valueOf(System.currentTimeMillis()));

        collectConfigParams(map, protocolConfig, basicService, extConfig, this);
        collectMethodConfigParams(map, this.getMethods());

        // {id=motan, export=motan:8002, protocol=motan, refreshTimestamp=1505801631928, group=motan-demo-rpc, nodeType=service, version=1.0}
        System.out.println(" 7 service param ==>" +map);
        URL serviceUrl = new URL(protocolName, hostAddress, port, interfaceClass.getName(), map);

        if (serviceExists(serviceUrl)) {
            throw new MotanFrameworkException();
        }

        List<URL> urls = new ArrayList<URL>();

        // injvm 协议只支持注册到本地，其他协议可以注册到local、remote
        if (MotanConstants.PROTOCOL_INJVM.equals(protocolConfig.getId())) {
            URL localRegistryUrl = null;
            for (URL ru : registryURLs) {
                if (MotanConstants.REGISTRY_PROTOCOL_LOCAL.equals(ru.getProtocol())) {
                    localRegistryUrl = ru.createCopy();
                    break;
                }
            }
            if (localRegistryUrl == null) {
                localRegistryUrl =
                        new URL(MotanConstants.REGISTRY_PROTOCOL_LOCAL, hostAddress, MotanConstants.DEFAULT_INT_VALUE,
                                RegistryService.class.getName());
            }
            urls.add(localRegistryUrl);
        } else {
            for (URL ru : registryURLs) {
                System.out.println(" 10 "+ ru);
                urls.add(ru.createCopy());
            }
        }

        for (URL u : urls) {
            u.addParameter(URLParamType.embed.getName(), StringTools.urlEncode(serviceUrl.toFullStr()));
            registereUrls.add(u.createCopy());
        }
        // [local://127.0.0.1:0/com.weibo.api.motan.registry.RegistryService?group=default_rpc]
        System.out.println(" 8 registereUrls ==>" +registereUrls);
        ConfigHandler configHandler = ExtensionLoader.getExtensionLoader(ConfigHandler.class).getExtension(MotanConstants.DEFAULT_VALUE);

        exporters.add(configHandler.export(interfaceClass, ref, urls));

        initLocalAppInfo(serviceUrl);
    }

    private void afterExport() {
        exported.set(true);
        for (Exporter<T> ep : exporters) {
            existingServices.add(ep.getProvider().getUrl().getIdentity());
        }
    }

    private void afterUnexport() {
        exported.set(false);
        for (Exporter<T> ep : exporters) {
            existingServices.remove(ep.getProvider().getUrl().getIdentity());
            exporters.remove(ep);
        }
        exporters.clear();
        registereUrls.clear();
    }

    @ConfigDesc(excluded = true)
    public BasicServiceInterfaceConfig getBasicService() {
        return basicService;
    }

    public void setBasicService(BasicServiceInterfaceConfig basicService) {
        this.basicService = basicService;
    }

    public Map<String, Integer> getProtocolAndPort() {
         
        return ConfigUtil.parseExport(this.export);
    }

    @ConfigDesc(excluded = true)
    public String getHost() {
        return host;
    }

   

}
 
```
1.  AbstractInterfaceConfig#loadRegistryUrls 用于收集,serviceconfig中设置的注册中心信息
   > motanDemoService.setRegistry(zookeeperRegistry);
   
 
 
 
 
 
 

#### 4. 启动nginx

执行命令： (重启 ，启动)
+ `nginx -s reload`
+  `nginx.exe`

#### 5. jd url与yhd代理url对应关系如下　
>请注意加精部分  /jd/api/  为yhd固定代码url前缀
  
+ jd域名：  http://rankcore.m.jd.local/rankInfo?
   +   {host}:/{**jd_path**}
   
+ 构造yhd代理域名
    + http://detail.yhd.com/jd/api/rankInfo
    + 代理域名path部分规则
	  + {host}:**/jd/ap**i/{**jd_path**}


#### 6. AJAX请求改造 codes

```html


   
```

#### 7. 结果
+ Chrome中访问如下URL
     
	>http://channel.yhd.com/jd/api/channel/rankInfo?body={%22cateId%22:%22655%22,%22provinceId%22:%221%22,%22time%22:%221DAY%22,%22rankId%22:%22rank3001%22}&clientVersion=6.2.0&build=38335&client=apple&d_brand=Xiaomi&d_model=RedmiNote2&osVersion=5.0.2&screen=1920*1080&partner=test&uuid=869043021004155-fc64bab32c82&area=12_904_905_50601&networkType=wifi&pin=txjjzyzqbx
     
	
+ 效果截图

	![](img/ajax_response.jpg)



 
    | JD URL  | YHD URL  |
    | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------- |
    | http://rankcore.m.jd.local/rankInfo?body={%22cateId%22:%22655%22,%22provinceId%22:%221%22,%22time%22:%221DAY%22,%22rankId%22:%22rank3001%22}&clientVersion=6.2.0&build=38335&client=apple&d_brand=Xiaomi&d_model=RedmiNote2&osVersion=5.0.2&screen=1920*1080&partner=test&uuid=869043021004155-fc64bab32c82&area=12_904_905_50601&networkType=wifi&pin=txjjzyzqbx  | http://detail.yhd.com/jd/api/channel/rankInfo?body={%22cateId%22:%22655%22,%22provinceId%22:%221%22,%22time%22:%221DAY%22,%22rankId%22:%22rank3001%22}&clientVersion=6.2.0&build=38335&client=apple&d_brand=Xiaomi&d_model=RedmiNote2&osVersion=5.0.2&screen=1920*1080&partner=test&uuid=869043021004155-fc64bab32c82&area=12_904_905_50601&networkType=wifi&pin=txjjzyzqbx  |
    | http://dynamic.item.jd.com/info/3846673.html  | http://detail.yhd.com/jd/api/detail/info/3846673.html  |

### 结束