# 1.mysql开启关闭安全模式

开启安全模式:          set sql_safe_updates=1;

关闭安全模式:          set sql_safe_updates=0;



## 2.mysql查询排序

从小到大      select * from 表名 order by cast(id  as signed integer) asc;

从大到小      select * from 表名 order by cast(id  as signed integer) desc;



# 3.mysql添加自增列

  use javaweb;
 alter table product modify id int unsigned auto_increment;



 ALTER TABLE Books AUTO_INCREMENT = 1000; 

# 4.mysql查看表结构

show create table javaweb;

# 4.sql查看端口号

exec sys.sp_readerrorlog 0, 1, 'listening'

# 5.mysql登录

 mysql -uroot **-h 127.0.0.1** -p 

# 6.mysql创建

ENGINE=InnoDB AUTO_INCREMENT=61 DEFAULT CHARSET=utf8mb4;

