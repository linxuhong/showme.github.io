                           Nginx AJAX 跨域请求
#### 1. 场景
-  网站www.a.com要通过ajax访问www.b.com的url, b域名不支持jsonp.方法有很多.这里采用Nginx处理
 
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
		 
		location ^~/jd/api/channel/{
			rewrite ^/jd/api/channel/(.*)$ /$1 break;
			proxy_pass http://rankcore.m.jd.local/;
		}
		
		location ^~/jd/api/detail/{
		    add_header 'Access-Control-Allow-Origin' '*';
            add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
            add_header 'Access-Control-Allow-Headers' 'DNT,X-CustomHeader,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type';
			
			rewrite ^/jd/api/detail/(.*)$ /$1 break;
			proxy_pass http://dynamic.item.jd.com/;
		}
		 
     }
 

 
#### 3. 绑定host
`
127.0.0.1  detail.yhd.com
   api_jd.yhd.com`

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
<html>
<head>
<script type="text/javascript" src="https://cdn.bootcss.com/jquery/1.5.1/jquery.js"></script>
<script type="text/javascript">
  
  /** Wrong  请求jd url无法跨域
  var jd ="http://rankcore.m.jd.local/rankInfo?body={%22cateId%22:%22655%22,%22provinceId%22:%221%22,%22time%22:%221DAY%22,%22rankId%22:%22rank3001%22}&clientVersion=6.2.0&build=38335&client=apple&d_brand=Xiaomi&d_model=RedmiNote2&osVersion=5.0.2&screen=1920*1080&partner=test&uuid=869043021004155-fc64bab32c82&area=12_904_905_50601&networkType=wifi&pin=txjjzyzqbx"
  $.getJSON(jd,function(data){
     alert("Data: " + data + "\nStatus: " + status);
  });
  */
   
  //Right 使用ajax sjon 请求yhd代理url  
  var yhdProxyUrl ="http://detail.yhd.com/jd/api/channel/rankInfo?body={%22cateId%22:%22655%22,%22provinceId%22:%221%22,%22time%22:%221DAY%22,%22rankId%22:%22rank3001%22}&clientVersion=6.2.0&build=38335&client=apple&d_brand=Xiaomi&d_model=RedmiNote2&osVersion=5.0.2&screen=1920*1080&partner=test&uuid=869043021004155-fc64bab32c82&area=12_904_905_50601&networkType=wifi&pin=txjjzyzqbx"
  $.get(yhdProxyUrl,function(data){
     alert("Data: " + data + "\nStatus: " + status);
  });
  
  //  JD详情页商品接口   http://dynamic.item.jd.com/info/3846673.html
  //  替换为yhd接口：    http://detail.yhd.com/jd/api/detail/info/3846673.html
 
</script>
</head>
<body>
</body>
</html>
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