
##    jenkis使用maven打包java 工程
>  当前目录 /usr/local/devtool

### 1.  【 新建任务】 或[ new item ] 
-   `选择maven项目，然后确定`
   ![下载包](1.png)

### 2. 配置SCM （git仓库地址）
    
-  `输入git项目地址，添加 用户名账号` 
  ![下载包](00.png)


### 3 . pre step
-    
```shell script
  echo " ************ begin to build project >>>>>>>>jobname is ${JOB_NAME}"
``` 

  ![下载包](2.png)


### 4. 配置maven执行参数  

- 配置参数    ` clean package`  

    ![下载包](3.png)
 

### 5. 打包完成
    
- 设置建完成后的操作  （归档文件名怎么生成，第6眇有说明）

   ![下载包](6.1.png)
   ![下载包](6.2.png)
    

### 6. 构建任务
 
- jenkins 首页点击任务 ，
    > **立即构建**
  
   ![下载包](7.png)
   
- 查看构建输出 :
     ************ begin to build project >>>>>>>>jobname is `build-git-maven`
     ![下载包](5.png)

     ![下载包](8.png)
   
   这个时候就可以知道第 5步中归档成品文件的名字了 `target/SpringBoot-Learning-1.0-SNAPSHOT.jar`

### 7. 查看结棍 
  ![下载包](9.png)
   