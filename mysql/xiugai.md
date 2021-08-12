# 基础规范
> 1. 在文件系统中，MySQL的数据存储区将以目录方式表示MySQL数据库。因此，上面命令中的数据库名字必须与操作系统的约束的目录名字一致。例如不允许文件和目录名中有\,/,:,*,?,”,<,>,|这些符号，在MySQL数据库名字中这些字母会被自动删除。<遵从目录的约束>
> 2. 数据库的名字不能超过64个字符，包含特殊字符的名字或者是全部由数字或保留字组成的名字必须用单引号``包起来。  
> 3.数据库不能重名。  
	
	select database();   查看自己所处的位置及默认所在的位置
	select now();        当前时间
	select user();          当前用户
	
	
	show  databases; 查询库；
	show databases \G 以行的方式显示
	show tables;     查询表；
	create databases ha;  新建新的库
	create database `HA-test1`; 新建新的库
	use ha 			选择要操作的数据库
	mysqL -uroot -p12345678  ha(库名) 
	drop database `HA-test`  删除数据库
	drop database if exitsts HA; 如果存在就删除
	drop database if not exists  如果不存在就删除
	
	
	create table student (id int(20),name varchar(40),age int);
	新建一个名为student的表。其中有 id name age的列  后面是数据参数
	desc student;   查看表结构 
	desc HA student;   查看表结构 
	show create table student \G 查询表信息
	create table student2 (id int(20),name varchar(40),age int)ENGINE=MyISAM DEFAULT CHARSET=utf8;
	新建表及指定存储引擎及字符集
	drop table student2;  删除表
	
	CHANGE 对列进行重命名和更改列的类型，需给定旧的列名称和新的列名称、当前的类型。 
	MODIFY 可以改变列的类型，此时不需要重命名（不需给定新的列名称）
	
	MariaDB [HA-test]> desc test;
	+-------+-------------+------+-----+---------+-------+
	| Field | Type        | Null | Key | Default | Extra |
	+-------+-------------+------+-----+---------+-------+
	| id    | int(10)     | YES  |     | NULL    |       |
	| name  | varchar(40) | YES  |     | NULL    |       |
	| age   | int(11)     | YES  |     | NULL    |       |
	+-------+-------------+------+-----+---------+-------+
	
	修改列数据
	alter  table student rename test;  修改表名
	alter  table test modify id int(10); 修改列数据
	alter table test change name stname char(20); 			修改列数据及属性
	alter table test add sex enum('M','W'); 				添加新列
	alter table test add uid int(10) first; 				添加为第一列
	alter table test add address char(40) after age; 		在age后面添加一个address字段：
	alter table test drop address;  						删除表中字段 alter table 表名 drop 字段名 ;
	
	
	
	插入字段 表数据
	insert into test values (1,'zhangsan',24,'M');
	MariaDB [HA-test]> select * from test;
	+------+----------+------+------+
	| id   | stname   | age  | sex  |
	+------+----------+------+------+
	|    1 | zhangsan |   24 | M    |
	+------+----------+------+------+
	
	mysql> insert into test values(2,'lis',24),(3,'wange',26);			插入多条分开
	mysql> insert into test (id,name)values(4,'hangl');                 指定字段插入
	
	
	mysql> select * from test;     					*表示所有
	mysql> select name from test;   				只查询表中某个字段的内容：
	mysql> select id,name from test;                多个字段内容,分割符,
	
	mysql> select *from HA.test;                    查看某个数据库下指定的表内容，数据库名.表名
	
	mysql> delete from test where id=3;          	删除id为3的行
	mysql> delete from tests where age is null; 	删除age为空的行
	mysql> update test set sex='M' where id=2;  	更新记录
	mysql> update test  set id=2;  					所有的都变为2
	mysql> update test set stname='zhangsan',age=21 where uid=1;  更新多个记录   ，逗号隔开。
	
	
	
	查询数据
	mysql> select name,age from test;
	mysql> select distinct name,age from test;  去重查询
	mysql> select  distinct id,name,age from test where id=3;
	MariaDB [HA-test]> select id,name,age from test where id>3 and age>21;  多条件查询 and 并且
	MariaDB [HA-test]> select id,name,age from test where id>3 and age>21;  多条件查询  or 或者
	select name from test where name='jk';  
	binary区分大小写
	select * from test where binary name='jk'; 区分大小写
	
	mysql> select distinct id from test order by id asc;	升序查询
	mysql> select distinct id from test order by id desc;   倒叙查询
	
	
	
	mysql数值类型
	数值
	日期 时间
	字符串
	
	
	mysqldump -uroot HA-test > ./test.sql  导出数据库
	
	mysql  -uroot test < ./test.sql  导入数据库
	mysql> source /root/book.sql  #sql脚本的路径
	Mysqldump –uroot –p123456 –B 库名>文件.sql
	-B : 导出整个库包含建库语句
	-A：导出全部数据库
	
	如何把一个select的结果导出到文本
	select * into outfile '/tmp/123.txt' from books; 此处有个文件访问权限问题，mysql用户是可以访问/tmp路径的,所以这里放到tmp下
	select * from books into outfile '/tmp/456.txt';
	扩展：  5.7版本导出报错，可以设置my.cnf 加上secure-file-priv="/ "
	
	
	算术运算符：
	=	等于
	<>	不等于  !=
	>	大于
	<	小于
	>=	大于等于
	<=	小于等于
	in	运算符
	IN 运算符用于 WHERE 表达式中，以列表项的形式支持多个选择，语法如下：
	WHERE column IN (value1,value2,...)
	WHERE column NOT IN (value1,value2,...)
	Not in 与in相反
	当 IN 前面加上 NOT 运算符时，表示与 IN 相反的意思，即不在这些列表项内选择。
	测试
	找出价格大于60的记录
	mysql> select bName,price from books where price>60;
	找出价格为60的
	mysql> select bName,price from books where price=60;
	找出价格不等于60的
	mysql> select bName,price from books where price<>60;
	找出价格是60，50，70的记录
	mysql> select bName,price from books where price in (50,60,70);
	找出价格不是60，50，70的记录
	mysql> select bName,price from books where price not in (50,60,70);
