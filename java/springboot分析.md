# spirngboot


    
     
###   

1.   java     -jar  Demo.jar

 在IDE工具的启动时，我们通常是run/debug 启动类SpBoot2MybatisApp
 将spring boot工程打成jar时，就是运行java -jar jarname.jar
 
 
   ```java
      
@SpringBootApplication
@MapperScan( {"com.xxx.*.mapper" }) 
public class SpBoot2MybatisApp {
    public static void main(String[] args) throws IOException {
		SpringApplication.run(SpBoot2MybatisApp.class, args);
	}
}  
      
``` 

2.  boot工程jar目录
  
 使用maven 打包成jar后，
  demo.jar
    
    - org
        └── org
            └── springframework
                └── boot
                    └── loader
                        ├── ExecutableArchiveLauncher.class
                        ├── JarLauncher.class
                        ├── JavaAgentDetector.class
                        ├── LaunchedURLClassLoader.class
                        ├── Launcher.class
                        ├── MainMethodRunner.class
                     
        
    - META-INF
       - MANIFEST.MF
    - BOOT-INF
       - lib
       - classes
       
MANIFEST.MF的内容如下
      
 ```java
         Start-Class: com.magicnet.SpBoot2MybatisApp
         Spring-Boot-Classes: BOOT-INF/classes/
         Spring-Boot-Lib: BOOT-INF/lib/
         Build-Jdk: 1.8.0_40
         Main-Class: org.springframework.boot.loader.JarLauncher
         
 
        
```  
  - Main-Class     JarLauncher 并不是工程中的主类 SpBoot2MybatisApp
  - Start-Class    SpBoot2MybatisApp 请注意


### springboot启动过程 

1. java -jar时，会查找 MANIFEST.MF的mian-class JarLauncher

2. 这个jar包与普通 的jar目录结构不太一样。   
    
在spring boot里，抽象出了Archive的概念。
一个archive可以是一个jar（JarFileArchive），也可以是一个文件目录（ExplodedArchive）。
可以理解为Spring boot抽象出来的统一访问资源的层。

  - demo-0.0.1-SNAPSHOT.jar 是一个Archive，
  - demo-0.0.1-SNAPSHOT.jar里的/lib目录下面的每一个Jar包，也是一个Archive。


    
    

2. JarLauncher 启动过程

-   
   

-  
   

  
  
# License

 



























