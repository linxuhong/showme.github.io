
#### 1. Nginx AJAX 跨域请求
-  网站www.yhd.com要通过ajax访问jd的url,jd域名不支持jsonp，方法有很多。这里采用nginx处理
 
[anchor-id]: http://nginx.org/en/

- Nginx Home  Page  

> 请自行查看 官方文档 http://nginx.org/en/




#### 2. 修改nginx  Nginx配置   
##### 配置文件目录  {nginx_install_directory}/config/nginx.conf 


      server {
         listen       80;
         
         server_name  detail.yhd.com  api_jd.yhd.com ;

         location / {
		     #add_header 'Access-Control-Allow-Origin' '*';
			 #add_header 'Access-Control-Allow-Credentials' 'true';
             root   html;
             index  index.html index.htm;
         }
		 
		 location ^~/jd/api/{
			rewrite ^/jd/api/(.*)$ /$1 break;
			proxy_pass http://rankcore.m.jd.local/;
		}
     }
 

#### 3. 启动nginx

执行命令： (重启 ，启动)
+ `nginx -s reload`
+  `nginx.exe`

#### 4. jd url与yhd代理url对应关系如下　
>请注意加精部分  /jd/api/  为yhd固定代码url前缀
  
+ jd域名：  http://rankcore.m.jd.local/rankInfo?
   +   {host}:/{**jd_path**}
   
+ 构造yhd代理域名
    + http://detail.yhd.com/jd/api/rankInfo
    + 代理域名path部分规则
	  + {host}:**/jd/ap**i/{**jd_path**}


#### 5. 直接请求 codes

```html
<html>
<head>
<script type="text/javascript" src="https://cdn.bootcss.com/jquery/1.5.1/jquery.js"></script>
<script type="text/javascript">
  
  
  var jd ="http://rankcore.m.jd.local/rankInfo?body={%22cateId%22:%22655%22,%22provinceId%22:%221%22,%22time%22:%221DAY%22,%22rankId%22:%22rank3001%22}&clientVersion=6.2.0&build=38335&client=apple&d_brand=Xiaomi&d_model=RedmiNote2&osVersion=5.0.2&screen=1920*1080&partner=test&uuid=869043021004155-fc64bab32c82&area=12_904_905_50601&networkType=wifi&pin=txjjzyzqbx"
  $.get(jd,function(data){
     alert("Data: " + data + "\nStatus: " + status);
  });
   
  // 使用yhd代理域名  
  var yhdProxyUrl ="http://detail.yhd.com/jd/api/rankInfo?body={%22cateId%22:%22655%22,%22provinceId%22:%221%22,%22time%22:%221DAY%22,%22rankId%22:%22rank3001%22}&clientVersion=6.2.0&build=38335&client=apple&d_brand=Xiaomi&d_model=RedmiNote2&osVersion=5.0.2&screen=1920*1080&partner=test&uuid=869043021004155-fc64bab32c82&area=12_904_905_50601&networkType=wifi&pin=txjjzyzqbx"
  $.get(yhdProxyUrl,function(data){
     alert("Data: " + data + "\nStatus: " + status);
  });
 
</script>
</head>
<body>
</body>
</html>
```


 

### 结束