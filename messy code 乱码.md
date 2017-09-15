  
 
 
## messy code 乱码问题 

#### 现象，有一个 url,
+ 在浏览器中访问,返回的JSON数据，中文正常
+ 使用ajax访问，一直返回乱码

### 乱码首先搜索，是ajax设置的编码方式不正确？
1. 乱码什么样子?   "����  
2. 百度后，看到如下方案,然而并没有作用
 
 
    $.ajax({   
      url: testUrl,   
      dataType: 'jsonp',   
      type: 'post',   
      scriptCharset: 'utf-8'  
    });  
    
    jQuery(form).ajaxSubmit({   
        url: "ajax.aspx?a=memberlogin",   
        type: "post",   
        dataType: "json",   
        contentType: "application/x-www-form-urlencoded; charset=utf-8",   
        success: showLoginResponse   
    });
    
#### 同时的url浏览器返回能正常识别其中的中文，
1. 问题1：浏览器是怎么知识 用正确的编码方式 来解析中文？
2. ajax设置UTF-8的方式肯定与服务端不相同 (别人的接口，找不到人，怎么办)
3. 中文的编码主要是 gbk,难道是 这个？，尝试了下果然是这个，那么服服务端使用的是GBK。。。

      $.ajax({   
          url: testUrl,   
          dataType: 'jsonp',   
          type: 'post',   
          scriptCharset: 'utf-8'  
        });  

#### 回到之前的问题，浏览器怎么知道用什么编码，然后解析成功了
 
 
 