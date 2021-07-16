# 1、基础操作

#### 1、SQL语句快捷键

> \G 格式化输出							\q											\c
>
> \s 查看服务器段信息				 \h											

#### 2、操作数据库

- 连接数据库：		`mysql -h 地址 -u用户名 -p密码 P(大写)端口号`
- 查看数据库：        `show databases;`
- 创建数据库：        `creat database 库名 default charset=utf-8;`
- 删库：                   `drop database 库名`;
- 打开数据库：       `use 库名;`

#### 3、操作数据表

- 查看数据表           `show tables;`

- 创建表：              `creat table 表名(id int not NULL primary key auto_increatment，字段2 类型,...) engine=innodb default charset=utf-8；`          

  ​                              字段约束：unsigned 、not null、 default、primary key、auto_increment、unique

- 若不存在创建表: `creat table if not exists 表名 （字段1 类型，字段2 类型，...）;`

- 删表：                 `drop table 表名；`

- 表结构：             `desc 表名;`

- 查看建表语句： `show creat table 表名;`

- 修改表结构

  ```mysql
  alter table 表名 add num int not null;#增加一个字段
  alter table 表名 add email varchar(50) after num;
  alter table 表名 add userId int first;#最前列加字段
  
  #删除字段
  alter table 表名 drop userId;
  
  #修改字段
  alter table 表名 change|modify 被修改的字段信息
  change: 可以修改字段名
  modify: 不能修改字段名。
  
  #修改表名
  alter table 原表名 rename as 新表名
  
  #在常规情况下，auto_increment 默认从1开始继续递增
  alter table users auto_increment = 1000;
  
  #修改表引擎语句
  alter table users engine = 'myisam';
  ```

#### 4、增删查改

- insert                  `insert into 表名（字段1，字段2，字段3，....）valuse(值1，值2，值3，...);`

  ​							`insert into 表名（字段1，字段2，字段3，...) values(值1，值2，值3，...)(值1，值2，值3，...);`	

- delete                 `delete from 表名 where...`

- select                  

  ```mysql
  select 字段列表|* from 表名
  [where ...]
  [group by 分组字段[having 分组条件]]
  [order by 排序字段 DESC ASC] 
  [limit offset,rows]
  
  #语法顺序
  select [distinct]
  from
  join（如left join）
  on
  where
  group by
  having
  union
  order by
  limit
  
  #执行顺序
  from 
  join 
  on 
  where 
  group by(开始使用select中的别名，后面的语句中都可以使用)
   avg,sum.... 
  having 
  select 
  distinct 
  order by
  limit 
  ```

- update               `update 表名 set字段=值 where...;`

  ​							`update 表名 set字段1=值，字段2=值  where...;`

#### 5、退出              `exit\quit`

#### 6、mysql数据类型

- 整型
  - Boolean 1\0
  - INT 、UNSIGNED INT
- 浮点型
  - double
  - float
- 字符串
  - 定长串char【1~255字符】
  - 变长串varchar 【1~255字节】
  - 变长文本Text 【64k】、LongText【4GB】、 TINYTEXT【255字节】
- 日期
  - DATE   YYYY-MM-DD
  - DATETIME  date和time组合
  - TIME HH:MM:SS
  - YEAR 70~69(1970-2069)、1901-2155

#### 7、运算符

- 算数运算符：       + - * / %
- 比较：                  =  > < >= <= !=
- 逻辑                      and or not
- 模糊查询               like  配合_ %

#### 8、存储引擎

- innodb 

  - 支持外键、事务
  - 叶子节点存数据
  - 行锁 
  - 没有设定主键或非空唯一索引，自动生成6字节主键（不可见）
  - 没有保存总行数 只能遍历

- myisam

  - 不支持事务
  - 叶子节点存地址
  - 只支持表锁
  - 允许无索引和主键的表存在
  - 保存了表总行数

- MyISAM和InnoDB两者的应用场景：

  ​		 MyISAM管理非事务表。它提供高速存储和检索，以及全文搜索能力。如果应用中需要执行大量的SELECT查询，那么MyISAM是更好的选择。 InnoDB用于事务处理应用程序，具有众多特性，包括ACID事务支持。如果应用中需要执行大量的INSERT或UPDATE操作，则应该使用InnoDB，这样可以提高多用户并发操作的性能。现在默认使用InnoDB。

#### 9、 字符集

#### 10、注意

- count(字段) 如果指定的列上出现了NULL值，那么为NULL的这个数据不会被统计；
- count(*) 是按照 users表中所有的列进行数据的统计，只要其中一列上有数据，就可以计算



#### 11、数据导出、导入

```mysql
#1、导出
# 数据库数据导出::::不要进入mysql，然后输入以下命令 导出某个库中的数据 会形成一个建表和添加语句组成的sql文件
mysqldump -u root -p tlxy > ~/Desktop/code/tlxy.sql;

#数据库中的表导出::::不要进入mysql，然后输入以下命令 导出某个库中指定的表的数据
mysqldump -u root -p tlxy tts > ~/Desktop/code/tlxy-tts.sql;

#2、导入
mysql -u root -p ops < ./tlxy.sql;
mysql -u root -p ops < ./tlxy-tts.sql;
```

#### 12、权限管理

```mysql
#grant 授权操作 on 授权的库.授权的表 to 账户@登陆地址 indentified by'密码'；
grant select,insert on tlxy.* to zhangsan@'%' indentified by'123456';




All/All Privileges权限代表全局或者全数据库对象级别的所有权限

Alter权限代表允许修改表结构的权限，但必须要求有create和insert权限配合。如果是rename表名，则要求有alter和drop原表， create和insert新表的权限

Alter routine权限代表允许修改或者删除存储过程、函数的权限

Create权限代表允许创建新的数据库和表的权限

Create routine权限代表允许创建存储过程、函数的权限

Create tablespace权限代表允许创建、修改、删除表空间和日志组的权限

Create temporary tables权限代表允许创建临时表的权限

Create user权限代表允许创建、修改、删除、重命名user的权限

Create view权限代表允许创建视图的权限

Delete权限代表允许删除行数据的权限

Drop权限代表允许删除数据库、表、视图的权限，包括truncate table命令

Event权限代表允许查询，创建，修改，删除MySQL事件

Execute权限代表允许执行存储过程和函数的权限

File权限代表允许在MySQL可以访问的目录进行读写磁盘文件操作，可使用的命令包括load data infile,select … into outfile,load file()函数

Grant option权限代表是否允许此用户授权或者收回给其他用户你给予的权限,重新付给管理员的时候需要加上这个权限

Index权限代表是否允许创建和删除索引

Insert权限代表是否允许在表里插入数据，同时在执行analyze table,optimize table,repair table语句的时候也需要insert权限

Lock权限代表允许对拥有select权限的表进行锁定，以防止其他链接对此表的读或写

Process权限代表允许查看MySQL中的进程信息，比如执行show processlist, mysqladmin processlist, show engine等命令

Reference权限是在5.7.6版本之后引入，代表是否允许创建外键

Reload权限代表允许执行flush命令，指明重新加载权限表到系统内存中，refresh命令代表关闭和重新开启日志文件并刷新所有的表

Replication client权限代表允许执行show master status,show slave status,show binary logs命令

Replication slave权限代表允许slave主机通过此用户连接master以便建立主从复制关系

Select权限代表允许从表中查看数据，某些不查询表数据的select执行则不需要此权限，如Select 1+1， Select PI()+2；而且select权限在执行update/delete语句中含有where条件的情况下也是需要的

Show databases权限代表通过执行show databases命令查看所有的数据库名

Show view权限代表通过执行show create view命令查看视图创建的语句

Shutdown权限代表允许关闭数据库实例，执行语句包括mysqladmin shutdown

Super权限代表允许执行一系列数据库管理命令，包括kill强制关闭某个连接命令， change master to创建复制关系命令，以及create/alter/drop server等命令

Trigger权限代表允许创建，删除，执行，显示触发器的权限

Update权限代表允许修改表中的数据的权限

Usage权限是创建一个用户之后的默认权限，其本身代表连接登录权限
```







​			