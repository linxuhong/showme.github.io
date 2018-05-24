MySQL group by filed_1 order by field_2 desc 每个分组取top 1的数据
====
## 一、需求

  需求爬起来比较笑意，根据uid分组，然后每个分组取id最大的那行数据

（2）建立测试数据

~~~mysql

    create table mytable(
       id int(4) primary key not null auto_increment,
       uid int not null,
       address varchar(32)   null
    )
	 
	insert into mytable (id,uid,address)
	values
	{1,110,'中国'},
	{2,110,'中国'},
	{3,119,'美国'},
	{4,119,'德国'}

~~~


## 第一个sql 
  `不满足要求`
  
~~~mysql

select max(id) as maxId ,uid,address 
from mytable
group by end_user_id

-----------------
maxId  uid address
2      110  中国
4      119  美国

~~~
  1. maxI取的最大的，uid也是对的  address的值一条正确，一条不正确
  

 ## 第二个sql 
  `不满足要求`
~~~mysql

select id, uid, address
from 
( 
    select id, uid, address
    from mytable
    group by uid
    order by id desc
  ) a
 group by uid 
having count(id) = 1
	 
~~~

验证后依旧不答要求

 ## 第三个sql  
 `满足要求`
 
 ~~~mysql
  select * from mytable 
  where id in
  (
      select max(id) 
      from mytable  group by uid
  
  )
  order by user_id;
 
 
~~~

## 最后一条sql
 `满足要求`

~~~mysql

 select a.id, b.uid, b.address
 from 
 (select max(id) id, uid
      from mytable
     group by uid
 ) a
join mytable b
on a.id = b.id
    
~~~

### 为什么前面两个sql 不满足要求，待完成
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 

 

## 二、命名规范
  
  
  
  
                    
>&copy; lxh_007@hotmail.com
 
  
  

