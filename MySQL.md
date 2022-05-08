# 知识大纲

![image-20210524191024256](img/MySQL.img/image-20210524191024256.png)

# MySQL常用命令

```shell
#开启
service mysql start

#关闭
service mysql stop

#重启
service mysql restart
```

```shell
#登录进入
mysql -u root -p 

#远程登录
mysql -h 10.0.8.7 -P 3306 -u root -p

#清屏
ctrl l
```

```mysql
#显示数据库
show databases; # 注意分号

#创建数据库
create database serverdb;

#删除数据库
drop database 数据库名;

#使用数据库
use serverdb;

#创建表
CREATE TABLE user(
    username char(50) NULL,
    passwd char(50) NULL
)ENGINE=InnoDB;

#添加数据
INSERT INTO user(username, passwd) VALUES('name', 'passwd');

#显示表
show tables;

#显示数据表结构
describe 数据表名;

#退出
quit 
exit
```

# MySQL数据类型

MySQL数据类型定义了数据的大小范围，因此使用时选择合适的数据类型，不仅会降低表占用的磁盘空间，间接减少了磁盘I/O的次数，提高了表的访问效率，而且索引的效率也和数据的类型息息相关

## 数值类型

<img align='left' src="img/MySQL.img/image-20210526111534563.png" alt="image-20210526111534563" style="zoom:60%;" />

> 注意：`age INT(9)`，括号里数字不是字节大小，整型占用内存的大小是固定的，和具体的类型是强相关的，括号里的数字(M)只是代表显示的宽度

## 字符串类型

数据库里字符串用单引号括起来

<img src="img/MySQL.img/image-20210526111953464.png" alt="image-20210526111953464" style="zoom:67%;" />

> char与varchar区别：
>
> - char 表示定长，长度固定，varchar表示变长，即长度可变。char如果插入的长度小于定义长度时，则用空格填充；varchar小于定义长度时，还是按实际长度存储，插入多长就存多长。
>
>   因为char长度固定，char的存取速度还是要比varchar要快得多，方便程序的存储与查找；但是char也为此付出的是空间的代价，因为其长度固定，所以会占据多余的空间，可谓是以空间换取时间效率。varchar则刚好相反，以时间换空间。
>
> - char最多存255个字符，varchar最多存65532个

## 日期和时间类型

<img align='left' src="img/MySQL.img/image-20210526113201496.png" alt="image-20210526113201496" style="zoom: 57%;" />

> TIMESTAMP会自动更新时间，非常适合那些需要记录最新更新时间的场景，而DATETIME需要手动更
> 新。  
>
> 返回当前系统时间：select now();
>
> 返回一个时间戳：select unix_timestamp(now());  #返回结果是一个从1970年开始的一个秒数

## enum和set

这两个类型，都是限制该字段只能取固定的值，但是枚举字段只能取一个唯一的值，而集合字段可以取任意个值

比如：性别只能取男/女：`sex enum('M', 'W') default 'M'  `

# MySQL运算符

**算术运算符：**+   -   *   /,DIV   %,MOD

**逻辑运算符：**与（AND,&&）、或（OR,||）、非（NOT,!）

**比较运算符：**

<img align='left' src="img/MySQL.img/image-20210526122054656.png" alt="image-20210526122054656" style="zoom:50%;" />

比较运算符使用示例：

```mysql
select * from user where sex='M' and score>=90.0;
select * from user where age between 20 and 22;
select * from user where score in (99.0, 100.0); 
select * from user where score is NOT NULL;
select * from user where name like 'zhang%';  /* zhang san, zhang na, zhang yang */
```

**通配符：**

%：替代一个或多个字符

_：仅替代一个字符

# 完整性约束

主键约束：primary key

自增性约束：auto_increment，设置自增了之后，添加字段时候会自动增大序号，删除之前的并不会复用小序号

唯一键约束：unique

非空约束：not null

默认值约束：default

外键约束：foreign key

# 表设计原则

一对一关系、一对多关系、多对多关系

<img align='left' src="img/MySQL.img/image-20210527123549386.png" alt="image-20210527123549386" style="zoom:50%;" />

# 范式

应用数据库范式可以带来许多好处，但是最重要的好处归结为三点：  

- 减少数据冗余（这是最主要的好处，其他好处都是由此而附带的）  
- 消除异常（插入异常，更新异常，删除异常）  
- 让数据组织的更加和谐  

但是数据库范式绝对不是越高越好，范式越高，意味着表越多，多表联合查询的机率就越大，SQL的效
率就变低。  遵循的范式越高，拆分的表就越多

**第一范式1NF：**每一列保持原子特性。注意，不符合第一范式不能称作关系型数据库。

**第二范式2NF：**属性完全完全依赖于主键（主要针对联合主键）

- 例如：选课关系表为SelectCourse(学号, 姓名, 年龄, 课程名称, 成绩, 学分)，（学号，课程名称）是联合
  主键，但是学分字段只和课程名称有关，和学号无关，相当于只依赖联合主键的其中一个字段，不符合
  第二范式。  

**第三范式3NF：**属性不依赖于其他非主属性（不存在传递依赖）

**BC范式BCNF：**每个表中只有一个候选键

**第四范式4NF：**消除表中的多值依赖

# 聚集函数

count、sum、avg、max、min

```mysql
select count(*) from user;  #返回user表中的记录数

select avg(grade) from SC where Cno='1'  #计算选修1号课程的学生平均成绩
```

注意：where子句中是不能用聚集函数作为条件表达式的，聚集函数只能用于==select子句和group by中的having子句==

# SQL语句

SQL：Structured Query Language，结构化查询语言，关系型数据库的通用语言。  

- 数据查询DQL（Data Query Language）：select
- 数据定义DDL（Data Definition Languages）：create，drop，alter（数据库、表、列、索引等数据库对象的定义）
- 数据操纵DML（Data Manipulation Language）：insert，update，delete（添加、删除、更新和查询数据库记录）
- 数据控制DCL（Data Control Language）：grant，revoke（控制不同的许可和访问级别的语句  ）

RDBMS：关系型数据库管理系统（MySQL，Oracle）

RDB：关系型数据库

table：二维表（行：记录；列：字段/属性）

## 库、表操作命令

**库操作**

```mysql
#显示数据库
show databases; # 注意分号

#创建数据库
create database 数据库名;

#删除数据库
drop database 数据库名;

#使用数据库
use serverdb;
```

**表操作**

```mysql
#创建表
CREATE TABLE user(
    username varchar(50) NOT NULL,  # 名称  类型  完整性约束条件 
    passwd varchar(50) NOT NULL,
    age tinyint not NULL
)ENGINE=InnoDB, default charset=utf8;

#添加数据
INSERT INTO user(username, passwd) VALUES('name', 'passwd');

#显示表
show tables;

#显示数据表结构
describe 数据表名;  /* deac name */

#删除表
drop table name;

#查看当前数据库中cur_name表的建表sql
show create table cur_name;

#退出
quit 
exit
```

## CRUD增查改删

- C：Create增加`CREATE TBL ...`，`insert into...`

- R：Retrieve查询`SELECT * from ...`

  <img align='left' src="img/MySQL.img/image-20210704095831843.png" alt="image-20210704095831843" style="zoom:30%;" />

- U：Update修改`UPDATE TBL ..SET ...`

- D：Delete删除`DELETE FROM TBL WHERE ...`

**增加insert**

```mysql
insert into user(nickname, name, age, sex) values('wang', 'li', 22, 'M');
insert into user(nickname, name, age, sex) values('666', 'li si', 21, 'W'), ('888', 'gao yang', 20, 'M');
```

<img src="img/MySQL.img/image-20210618113400360.png" alt="image-20210618113400360" style="zoom:45%;" />

**查询select**

==注意：字符串使用通配符时候得使用like，使用null/not null得使用is==

```mysql
# 带通配符的匹配  
select name,age,sex from user where name like "zhang%";
select name,age,sex from user where name is not null;
```

```mysql
select * from user;
select id,nickname,name,age,sex from user;
select id,name from user;

select id,nickname,name,age,sex from user where sex='M' and age>=20 and age<=25;
select id,nickname,name,age,sex from user where sex='M' and age between 20 and 25;
select id,nickname,name,age,sex from user where sex='W' or age>=22;
```

**修改update**

```mysql
update user set age=23 where name='zhangsan'
update user set age=age+1 where id=3;
```

**删除delete**

```mysql
delete from user where age=23;
delete from user where age between 20 and 22;
delete from user;  /* 删除所有行 */
```

**去重distinct**

```mysql
select distinct name from user;
```

**空值/非空值查询**

```mysql
select * from user where name is null;  #非空值：is not null
```

**合并查询union**

```mysql
SELECT expression1, expression2, ... expression_n
FROM tables[WHERE conditions]
UNION [ALL | DISTINCT] # 注意：union默认去重，不用修饰distinct，all表示显示所有重复值
SELECT expression1, expression2, ... expression_n
FROM tables[WHERE conditions];

select country from Websites
UNION ALL  #all显示所有重复值
select country from apps order by country;
```

**带in子查询**

```mysql
select * from user where id in(10, 20, 30, 40, 50)
select * from user where id not in(10, 20, 30, 40, 50)
select * from user where id in(select stu_id from grade where average>=60.0)
```

**排序order by**

```mysql
select id,nickname,name,age,sex from user where sex='M' and age>=20 and age<=25
order by age asc;  # 升序

select id,nickname,name,age,sex from user where sex='M' and age>=20 and age<=25
order by age desc;  # 降序

select * from user order by name,age desc;  #如果name相同再按age排序
```

**分组group by**

GROUP BY 语句用于结合合计函数，根据一个或多个列对结果集进行分组

直接`select 某列 from table group by 某列`只会显示该分组的第一条记录，所以一般group by都是和count合用来统计组内的数量

![image-20210708164130958](img/MySQL.img/image-20210708164130958.png)

```mysql
select sex from user group by sex;
select count(id), sex from user group by sex;  #查询不同性别的人数，count函数返回记录数
select count(id), age from user group by age having age>20;  #对group by的结果过滤使用having
```

group by多和统计count配合使用：

<img align='left' src="img/MySQL.img/image-20210702180841953.png" alt="image-20210702180841953" style="zoom:40%;" />

**having过滤分组**

```mysql
select id,count(*) as nums
from orders
group by id
having count(*)>=2
```

<img align='left' src="img/MySQL.img/image-20210709093610398.png" alt="image-20210709093610398" style="zoom:50%;" />

**笔试实践**

<img align='left' src="img/MySQL.img/image-20210529122413026.png" alt="image-20210529122413026" style="zoom:50%;" />

```mysql
#统计表中缴费的总笔数和总金额
select count(serno), sum(amount) from bank_bill 

#按网点和日期统计每个网点每天的营业额，并按照营业额进行倒叙排序
select brno,date,sum(amount) as money from bank_bill group by brno,date order by brno,money desc;
```

![image-20210702183920270](img/MySQL.img/image-20210702183920270.png)

![image-20210702183842892](img/MySQL.img/image-20210702183842892.png)

## explain查看执行计划

在查询语句前面加explain能够查看SQL语句查询的一些计划/信息

```mysql
explain select * from user where name='wang';
```

![image-20210702163634116](img/MySQL.img/image-20210702163634116.png)

注意：explain看不到mysql的一些优化，比如使用`select * from user where age=20 limit 1;`去查找，mysql其实找到一条符合条件的就会结束查找，但是使用explain看到的rows那里还是会显示扫描了所有行数

## limit分页查询

**limit 子句可以被用于强制 select 语句返回指定的记录数**

```mysql
#如果只给定一个参数，它表示返回最大的记录行数目：    
SELECT * FROM table LIMIT 5; #检索前 5 个记录行  

#两个参数：第一个参数是偏移量，第二个参数是检索的行数
SELECT * FROM table LIMIT 5,10; # 偏移5个，后面的10个返回，即：6-15行

#上面两个参数的语法等价于：
select * from table limit 10 offset 5   # 偏移5个，后面的10个返回，即：6-15行

#为了检索从某一个偏移量到记录集的结束所有的记录行，可以指定第二个参数为 -1：    
SELECT * FROM table LIMIT 95,-1; #检索记录行 96-last.   
```

<img align='left'  src="img/MySQL.img/image-20210702171459760.png" alt="image-20210702171459760" style="zoom:50%;" />

**limit可以提高查询的效率，如下图：（因为如果找完limit限制的记录数，就不会继续扫描了）**

<img align='left' src="img/MySQL.img/image-20210702170426189.png" alt="image-20210702170426189" style="zoom:30%;" />

**使用limit的两个参数用法时，偏移第一个参数的时候有时效率不高，可以使用某个主键的where条件来优化：**

```mysql
select * from user limit 1000000, 20  #偏移1000000个，返回20个
#优化后：
select * from user where id > 1000000 limit 20 #因为id是主键，所以where条件很快
```

<img align='left' src="img/MySQL.img/image-20210702173113783.png" alt="image-20210702173113783" style="zoom:40%;" />

## 连接查询

**内连接**

<img align='left' src="img/MySQL.img/image-20210703120707886.png" alt="image-20210703120707886" style="zoom:37%;" />

```mysql
#内连接：inner join
SELECT a.属性名1,a.属性名2,...,b,属性名1,b.属性名2... FROM table_name1 a 
inner join table_name2 b on a.id = b.id   #inner可以省略，默认join就是内连接
where a.属性名 满足某些条件;

#on a.uid = c.uid 区分大表和小表，按照数量来区分，小表永远是整表扫描，然后去大表扫描，所以给大表建索引是有用的
#从stuent小表中取出所有的a.uid，然后拿着这些uid去大表中搜索（大表中uid加索引提高效率）
select a.uid,a.name,a.age,a.sex,c.score from student a  #student a 即 student as a，as可以省略
inner join exam c on a.uid=c.uid
where c.uid=1 and c.uid=2;
```

![image-20210703132004737](img/MySQL.img/image-20210703132004737.png)

![image-20210703132748748](img/MySQL.img/image-20210703132748748.png)

**自己和自己内连接来优化查询的应用场景：**

如对于：`select * from t_user limit 1500000,10`，可以使用`select * from t_user where id > 1500000 limit 10`来提高效率，但是有时候不知道id具体的条件，无法使用where子句来查，则可以通过以下自己和自己做内连接来调高效率：（id是主键，id的limit 1500000比所有属性进行limit 1500000快，然后再从小表中取出所有的id，然后拿着这些id去大表中搜索，因为id是主键所有查询是常数级别）

```mysql
#自己和自己的临时表内连接
select a.id,a.email,a.password from t_user a inner join(select id from from t_user limit 1500000,10) b on a.id=b.id;
```

**左/右外连接**

通过外连接可以判断哪些属性是NULL

```mysql
# 左连接：left outer join，outer可以省略
# 先对左表进行整表扫描，把left这边的表所有的数据显示出来，在右表中不存在相应数据，则显示NULL
select a.* from User a left outer join Orderlist b on a.uid=b.uid where
a.orderid is null;
```

```mysql
# 右连接：right outer join，outer可以省略
# 先对右表进行整表扫描，把right这边的表所有的数据显示出来，在左表中不存在相应数据，则显示NULL
select a.* from User a right outer join Orderlist b on a.uid=b.uid where
b.orderid is null;
```

**左连接、右连接、内连接、全外连接区别**

- left join：在两张表进行连接查询时，会返回左表所有的行，即使在右表中没有匹配的记录。

- right join：在两张表进行连接查询时，会返回右表所有的行，即使在左表中没有匹配的记录。

- inner join：在两张表进行连接查询时，只保留两张表中完全匹配的结果集。

- full join：在两张表进行连接查询时，返回左表和右表中所有没有匹配的行。

  ![image-20210519172343524](img/MySQL.img/image-20210519172343524.png)

> [[left join,right join,inner join,full join之间的区别](https://www.cnblogs.com/lijingran/p/9001302.html)]

**左连接示例：**

![image-20210703163003915](img/MySQL.img/image-20210703163003915.png)

**注意：连接查询里的where和on**

对于inner join内连接，过滤条件写在where的后面和on连接条件里面，效果是一样的，mysql会把on的条件翻译成where（因为where后面的过滤条件可以通过索引来进行过滤），但是对于外连接来说是不一样的：

```mysql
select a.* from student a inner join exame b on a.uid=b.uid where b.cid=3;
select a.* from student a left join exame b on a.uid=b.uid where b.cid=3;
select a.* from student a left join exame b on a.uid=b.uid and b.cid=3;
```

![image-20210703165554507](img/MySQL.img/image-20210703165554507.png)

上图的sql语句查询结果是一样的，因为left join后面的where条件会先执行，而并不是按左连接那样先去扫描左表，所以这样写就和内连接查询结果一样了

如下图这样写就可以得到正确的左连接查询结果：

![image-20210703165906045](img/MySQL.img/image-20210703165906045.png)

**具体sql执行信息可以通过expalin来查看：**

```mysql
select a.* from student a inner join exame b on a.uid=b.uid where b.cid=3;
select a.* from student a left join exame b on a.uid=b.uid where b.cid=3;
```

如下图：这两句语句的执行是相同的：都是先using where来过滤b表，然后b表就是一个小表，再拿小表去大表a里面搜索

![image-20210703171113017](img/MySQL.img/image-20210703171113017.png)

当把过滤条件换成and之后：

```mysql
select a.* from student a left join exame b on a.uid=b.uid and b.cid=3;
```

如下图：会先对左表a做整表扫描，符合左连接查询的流程

![image-20210703171017614](img/MySQL.img/image-20210703171017614.png)

**因此：**

使用外连接查询的时候要留意：过滤条件使用on...and这样的而不是where，where的条件只用来作null值判断

# MySQL的存储引擎

数据库存储引擎：是数据库底层软件组织，数据库管理系统（DBMS）使用数据引擎进行创建、查询、更新和删除数据。不同的存储引擎提供不同的存储机制、索引技巧、锁定水平等功能，使用不同的存储引擎，还可以获得特定的功能。现在许多不同的数据库管理系统都支持多种不同的数据引擎。MySQL的核心就是插件式存储引擎。

 <img align='left' src="img/MySQL.img/image-20210506174012219.png" alt="image-20210506174012219" style="zoom:30%;" />













使用`show engines;`可以查看存储引擎

![image-20210708160223676](img/MySQL.img/image-20210708160223676.png)

## MyISAM

MyISAM 不支持事务、也不支持外键，索引采用非聚集索引，其优势是访问的速度快，对事务完整性没有要求，以 SELECT、INSERT 为主的应用基本上都可以使用这个存储引擎来创建表。

MyISAM的表在磁盘上存储成 3 个文件，其文件名都和表名相同，扩展名分别是：

- .frm（存储表定义）
- .MYD（MYData，存储数据）
- .MYI （MYIndex，存储索引）  

**MyISAM的索引**

MyISAM引擎使用B+树作为索引结构，==叶节点的data域存放的是数据记录的地址（非聚集索引）==。下图是MyISAM主键索引的原理图：

<img align='left' src="img/MySQL.img/image-20210529132741262.png" alt="image-20210529132741262" style="zoom:40%;" />  















MyISAM存储引擎，索引结构叶子节点存储关键字和数据地址，也就是说索引关键字和数据没有在一起存放，体现在磁盘上，就是索引在一个文件存储，数据在另一个文件存储，例如一个user表，会在磁盘上存储三个文件 

- user.frm（表结构文件） 
- user.MYD（表的数据文件） 
- user.MYI（表的索引文件）

MyISAM的索引方式也叫做非聚集索引。  

## InnoDB

InnoDB 存储引擎提供了具有提交、回滚和崩溃恢复能力的事务安全，支持自动增长列，外键等功能，索引采用聚集索引，索引和数据存储在同一个文件，所以InnoDB的表在磁盘上有两个文件，其文件名都和表名相同，扩展名分别是：

- .frm（存储表的定义）
- .ibd（存储数据和索引）  

**InnoDB的索引**

InnoDB存储引擎的主键索引，==叶子节点中，索引关键字和数据是在一起存放的（聚集索引）==，如图：  

<img align='left' src="img/MySQL.img/image-20210529133023465.png" alt="image-20210529133023465" style="zoom:50%;" />

InnoDB的索引关键字和数据都是在一起存放的，体现在磁盘存储上，例如创建一个user表，在磁盘上只存储两种文件，

- user.frm（存储表的结构）
- user.ibd（存储索引和数据）。  

InnoDB的索引树叶节点包含了完整的数据记录，这种索引叫做聚集索引。因为InnoDB的数据文件本身要按主键聚集，所以InnoDB要求表必须有主键（区别于MyISAM可以没有），如果没有显式指定，则MySQL系统会自动选择一个可以唯一标识数据记录的列作为主键，如果不存在这种列，则MySQL自动为InnoDB表生成一个隐含字段作为主键，这个字段长度为6个字节，类型为长整形。  

## MEMORY

MEMORY 存储引擎使用存在内存中的内容来创建表。每个MEMORY 表实际只对应一个磁盘文件，格式是.frm（表结构定义）。

MEMORY 类型的表访问非常快，因为它的数据是放在内存中的，并且==默认使用HASH索引==（不适合做范围查询），但是一旦服务关闭，表中的数据就会丢失掉。  

## 各种存储引擎区别

<img align='left' src="img/MySQL.img/image-20210529124549285.png" alt="image-20210529124549285" style="zoom:50%;" />

**InnoDB与MyISAM区别：**

**相同点：**索引都是B+树

**存储结构的区别：**

- MyISAM：表格式，叶子结点上的数据项存的是引用（指针），数据量大的时候用myisam。myisam的B+树索引都是在内存中的，所以myisam可以作全文索引。因为叶子结点存的是指针，所以会有二次寻址。
- InnoDB：数据项实实在在存储在叶子结点上面，数据量小的时候用innodb。innodb的B+树索引的部分叶子结点在内存中，不是全部都在内存

**对于事务的支持：**

- MyISAM：MyISAM不支持事务
- InnoDB：InnoDB支持事务，对数据安全性要求比较高使用InnoDB

**锁的区别：**

- MyISAM：myisam采用的是表级锁

- InnoDB：InnoDB采用的是行级锁，同时也支持表级锁。默认使用的是行级锁，当查找失败的时候，行级锁转变为表级锁

![image-20210506215607189](img/MySQL.img/image-20210506215607189.png)

 

# T33MySQL索引

索引：为了更快地查找到数据

优点： 提高查询效率

缺点： 索引并非越多越好，过多的索引会导致CPU使用率居高不下，由于数据的改变，会造成索引文件的改动，过多的磁盘I/O造成CPU负荷太重  

## 索引分类

索引类型：按逻辑分类、按物理分类

逻辑分类：

- 主键索引：使用Primary Key修饰的字段会自动创建索引(MyISAM, InnoDB)  。每张表中的主键索引只能有一个，要求主键中的每个值都唯一，即不可重复，也不能有空值。
- 唯一索引：使用UNIQUE修饰的字段，值不能够重复，主键索引就隶属于唯一性索引。数据列不能有重复，可以有空值。一张表可以有多个唯一索引，但是每个唯一索引只能有一列。如身份证，卡号等。
- 普通索引：一张表可以有多个普通索引，可以重复可以为空值
- 全文索引：可以加快模糊查询，不常用
- 单列索引：在一个字段上创建索引  
- 多列索引：在表的多个字段上创建索引 (uid+cid，多列索引必须使用到第一个列，才能用到多列索
  引，否则索引用不上)  

物理分类：

- 聚集索引（聚簇索引）：数据在物理存储中的顺序跟索引中数据的逻辑顺序相同，比如以ID建立聚集索引，数据库中id从小到大排列，那么物理存储中该数据的内存地址值也按照从小到大存储。一般是表中的主键索引，如果没有主键索引就会以第一个非空的唯一索引作为聚集索引。一张表只能有一个聚集索引。
- 非聚集索引：数据在物理存储中的顺序跟索引中数据的逻辑顺序不同。非聚集索引因为无法定位数据所在的行，所以需要扫描两遍索引树。第一遍扫描非聚集索引的索引树，确定该数据的主键ID，然后到主键索引（聚集索引）中寻找相应的数据

## 索引创建和删除

创建表的时候指定索引字段：

```mysql
create table name(id int,
                 name varchar(20),
                 sex enum('m','w'),
                 index(id, name));
```

在已经创建的表上添加索引：

```mysql
create [unique] index 索引名 on 表名 (属性(length) [ASC|DESC]);
```

删除索引：

```mysql
drop index 索引名 on 表名;
```

## 索引的执行过程

explain分析执行过程

## 索引的底层原理、B与B+区别

**MySQL数据库索引采用的是B+树**

MySQL支持两种索引，一种的B-树索引，一种是哈希索引，大家知道，B-树和哈希表在数据查询时的效率是非常高的。
这里我们主要讨论一下MySQL InnoDB存储引擎，基于B-树（但实际上MySQL采用的是B+树结构）的索引结构。

B-树是一种m阶平衡树，叶子节点都在同一层，由于每一个节点存储的数据量比较大，索引整个B-树的
层数是非常低的，基本上不超过三层。

由于磁盘的读取也是按block块操作的（内存是按page页面操作的），因此B-树的节点大小一般设置为
和磁盘块大小一致，这样一个B-树节点，就可以通过一次磁盘I/O把一个磁盘块的数据全部存储下来，
所以当使用B-树存储索引的时候，磁盘I/O的操作次数是最少的（MySQL的读写效率，主要集中在磁盘
I/O上）  

**B树B+树区别、B+树比B树更适合索引的原因：**

![image-20210529131647585](img/MySQL.img/image-20210529131647585.png)

- B-树的每一个节点，存了关键字和对应的数据地址，而B+树的非叶子节点只存关键字，不存数据地址。因此B+树的每一个非叶子节点存储的关键字是远远多于B-树的，B+树的叶子节点存放关键字和数据，因此，从树的高度上来说，==B+树的高度要小于B-树，使用的磁盘I/O次数少，因此查询会更快一些==。  
- B-树由于每个节点都存储关键字和数据，因此离根节点进的数据，查询的就快，离根节点远的数据，查询的就慢；B+树所有的数据都存在叶子节点上，因此在B+树上搜索关键字，找到对应数据的==时间是比较平均的==，没有快慢之分 
- 在B-树上如果做区间查找，遍历的节点是非常多的；B+树所有叶子节点被连接成了有序链表结构，因此做==整表遍历和区间查找==是非常容易的   

> 举个例子：假设磁盘中的一个盘块容纳16Bytes，而一个关键字2Bytes，一个关键字具体信息指针2Bytes。一棵9阶的B-tree（一个结点最多8个关键字）的内部结点需要2个盘块。而B+树内部结点只需要1个盘块。
>
> 当需要把内部结点读入内存中的时候，B树就比B+树多一次盘块查找时间（在磁盘中就是盘片旋转的时间）

说到底就是因为：B+树一个非终端结点只存索引项（不存具体信息指针），所以与B树同样大小的结点，B+树可以比B树存更多的索引项，所以在找某一个索引项的时候，B+树的磁盘IO更少，也就更快，并且B+树更有利于基于范围的查询

# MySQL事务

**什么是事务**

事务就是一组逻辑操作的集合。

- 事务：一条或者多条对数据库操作的SQL语句所组成的一个不可分割的单元  
- 事务的所有SQL语句全部执行成功，才能提交（commit）事务，把结果写回磁盘上。  
- 事务执行过程中，有的SQL出现错误，那么事务必须要回滚（rollback）到最初的状态  

**怎么实现事务**

实现事务就是要保证可靠性和并发隔离，或者说，能够满足ACID特性的机制。而这些主要是靠日志恢复和并发控制实现的

- 日志恢复：数据库里有两个日志，一个事redo log，一个事undo log。redo log记录的是已经成功提交的事务操作信息，用来恢复数据，保证事务的**持久性**。undo log记录的是事务修改之前的数据信息，用来回滚数据，保证事务的**原子性**。
- 并发控制：并发控制主要靠读写锁和MVCC（多版本控制）来实现。读写锁包括共享锁和排它锁，保证事务的**隔离性**。MVCC通过为数据添加时间戳来实现

**事务的ACID特性**

- 原子性（Atomic）：事务是一个不可分割的工作单位，这组操作要么全部发生，要么全部不发生
- 一致性（Consistency）：在事务开始以前，数据库中的数据有个一致的状态。在事务完成以后，数据库中的事务也应该保持这种一致性。即事务应该将数据从一个一致性状态移动到另一个一致性状态
- 隔离性（Isolation）：数据库中的事务不会受另一个并发执行的事务的影响
- 持久性（Durability）：事务对数据库的改变是永久的。事务完成(commit)以后，DBMS保证它对数据库中的数据的修改是永久性的，即使数据库因为故障出错，也应该能够恢复数据！  

**事务并发存在的问题**

- 脏读（Dirty Read）：脏读是指一个事务在处理过程中读取了另一个还没提交的事务的数据。
- 不可重复读（NonRepeatable Read）：不可重复读是对于数据库中的某一个字段，一个事务多次查询却返回了不同的值，这是由于在查询的间隔中，该字段被另一个事务修改并提交了。
- 幻读（Phantom Read  ）：事务多次读取同一个范围的时候，查询结果的记录数不一样，这是由于在查询的间隔中，另一个事务新增或删除了数据。

**事务的隔离级别**

为了保证数据库事务一致性，解决脏读，不可重复读和幻读的问题，数据库的隔离级别一共有四种隔离级别：

- 读未提交：最低级别的隔离，不能解决以上问题
- 读已提交：只允许读已提交的数据，可以避免脏读的发生
- 可重复读：确保事务可以多次从一个字段中读取相同的值，在该事务执行期间，禁止其他事务对此字段的更新，可以避免脏读和不可重复读。 通过锁行来实现
- 串行化：最严格的事务隔离机制，要求所有事务被串行执行，可以避免以上所有问题。 通过锁表来实现

| 隔离级别 | 脏读   | 不可重复度 | 幻读   |
| -------- | ------ | ---------- | ------ |
| 读未提交 | 未解决 | 未解决     | 未解决 |
| 读已提交 | 解决   | 未         | 未     |
| 可重复读 | 解决   | 解决       | 未     |
| 串行化   | 解决   | 解决       | 解决   |

Oracle的默认隔离级别是**读已提交**，实现了四种隔离级别中的读已提交和串行化隔离级别

MySQL的默认隔离级别是**可重复读**，并且实现了所有四种隔离级别

**MySQL事务处理的命令**

```mysql
# 查看MySQL是否自动提交事务
select @@autocommit;  # 0表示手动提交事务，1表示自动提交事务

# 设置事务提交方式为手动提交方式
set autocommit=0;

# 开启一个事务
BEGIN

#提交一个事务
COMMIT

# 回滚一个事务到初始的位置
ROLLBACK

# 设置一个名字为point1的保存点
SAVEPOINT point1; 

# 事务回滚到保存点point1，而不是回滚到初始状态
ROLLBACK TO point1;

# 设置事务的隔离级别
SET TX_ISOLATION='REPEATABLE-READ'; 

# 查询事务的隔离级别
SELECT @@ TX_ISOLATION;
```

# MySQL的锁机制

**表级锁、行级锁**

- 表级锁：对整张表加锁。开销小，加锁快，不会出现死锁；锁粒度大，发生锁冲突的概率高，并发度低
- 行级锁：对某行记录加锁。开销大，加锁慢，会出现死锁；锁定粒度最小，发生锁冲突的概率最低，并发度高。    

**排他锁、共享锁**

- 共享锁：Shared，又称为S 锁，读锁  ，读操作的时候创建的锁，一个事务对数据加上共享锁之后，其他事务只能对数据再加共享锁，不能进行写操作直到释放所有共享锁。
- 排他锁：Exclusive，又称为X 锁，写锁  ，写操作时创建的锁，事务对数据加上排他锁之后其他任何事务都不能对数据加任何的锁（即其他事务不能再访问该数据）

**MyISAM表级锁**

- MyISAM存储引擎不支持事务处理，因此它的并发比较简单，只支持到表锁的粒度，粒度比较大，并发能力一般，但不会引起死锁的问题，它支持表级共享的读锁和互斥的写锁  
- 对 MyISAM 表的读操作（共享锁），不会阻塞其他用户对同一表的读请求，但会阻塞对同一表的写请求；对 MyISAM 表的写操作（排它锁），则会阻塞其他用户对同一表的读和写操作。  
- MyISAM 表的读操作与写操作之间，以及写操作之间是串行的。  
- MyISAM 在执行查询语句（SELECT）前，会自动给涉及的所有表加读锁，在执行更新操作（UPDATE、DELETE、INSERT 等）前，会自动给涉及的表加写锁，这个过程并不需要用户控制，是MySQL Server端自动完成的。  

**InnoDB行级锁**

- InnoDB存储引擎支持事务处理，表支持行级锁定，并发能力更好。  
- InnoDB行锁是通过给索引上的索引项加锁来实现的，而不是给表的行记录加锁实现的，这就意味着只有通过索引条件检索数据，InnoDB才使用行级锁，否则InnoDB将使用表锁。  
- 由于InnoDB的行锁实现是针对索引字段添加的锁，不是针对行记录加的锁，因此虽然访问的是InnoDB引擎下表的不同行，但是如果使用相同的索引字段作为过滤条件，依然会发生锁冲突，只能串行进行，不能并发进行  

- 即使SQL中使用了索引，但是经过MySQL的优化器后，如果认为全表扫描比使用索引效率更高，此时会放弃使用索引，因此也不会使用行锁，而是使用表锁，比如对一些很小的表，MySQL就不会去使用索引

**InnoDB表级锁**

- 在绝大部分情况下都应该使用行锁，因为事务和行锁往往是选择InnoDB的理由，但个别情况下也使用表级锁：
  - 事务需要更新大部分或全部数据，表又比较大，如果使用默认的行锁，不仅这个事务执行效率低，
    而且可能造成其他事务长时间等待和锁冲突；  
  - 事务涉及多个表，比较复杂，很可能引起死锁，造成大量事务回滚。  

**间隙锁**

- 当我们用范围条件而不是相等条件检索数据，并请求共享或排他锁时，InnoDB 会给符合条件的已有数据记录的索引项加锁；对于键值在条件范围内但并不存在的记录，叫做“间隙（GAP)” ，InnoDB 也会对这个“间隙”加锁，这种锁机制就是所谓的间隙锁。

- 举例来说， 假如 user 表中只有 101 条记录， 其userid 的值分别是 1,2,...,100,101， 下面的 SQL：  

  `select * from user where userid > 100 for update;`这是一个范围条件的检索，InnoDB 不仅会对符合条件的 userid 值为 101 的记录加锁，也会对userid 大于 101（但是这些记录并不存在）的"间隙"加锁，防止其它事务在表的末尾增加数据  

- InnoDB使用间隙锁的目的，是为了防止幻读，以满足串行化隔离级别的要求，对于上面的例子，要是不使用间隙锁，如果其他事务插入了 userid 大于 100 的任何记录，那么本事务如果再次执行上述语句，就会发生幻读。  

**意向共享锁和意向排他锁**

- 意向共享锁（IS锁）：事务计划给记录加行共享锁，事务在给一行记录加共享锁前，必须先取得该表的IS 锁。
- 意向排他锁（IX锁）：事务计划给记录加行排他锁，事务在给一行记录加排他锁前，必须先取得该表的IX 锁    

**死锁**

- MyISAM 表锁是 deadlock free 的， 这是因为 MyISAM 总是一次获得所需的全部锁，要么全部满足，要么等待，因此不会出现死锁。但在 InnoDB 中，除单个 SQL 组成的事务外，锁是逐步获得的，即锁的粒度比较小，这就决定了在 InnoDB 中发生死锁是可能的  
- 死锁问题一般都是我们自己的应用造成的，和多线程编程的死锁情况相似，大部分都是由于我们多个线程在获取多个锁资源的时候，获取的顺序不同而导致的死锁问题。因此我们应用在对数据库的多个表做更新的时候，不同的代码段，应对这些表按相同的顺序进行更新操作，以防止锁冲突导致死锁问题  

**锁的优化建议**

- 尽量使用较低的隔离级别
- 设计合理的索引并尽量使用索引访问数据，使加锁更加准确，减少锁冲突的机会提高并发能力  
- 选择合理的事务大小，小事务发生锁冲突的概率小  
- 不同的程序访问一组表时，应尽量约定以相同的顺序访问各表，对一个表而言，尽可能以固定的顺序存取表中的行。这样可以大大减少死锁的机会  
- 尽量用相等条件访问数据，这样可以避免间隙锁对并发插入的影响  
- 不要申请超过实际需要的锁级别  
- 除非必须，查询时不要显示加锁  

**MVCC多版本并发控制**

- Multi-Version Concurrency Control，  多版本并发控制。是MySQL中基于乐观锁理论实现隔离级别的方式，用于实现已提交读和可重复读隔离级别的实现，也经常称为多版本数据库。
- MVCC机制会生成一个数据请求时间点的一致性数据快照 （Snapshot)， 并用这个快照来提供一定级别 （语句级或事务级） 的一致性读取。从用户的角度来看，好象是数据库可以提供同一数据的多个版本（系统版本号和事务版本号）。  
- 每一行记录实际上有多个版本，每个版本的记录除了数据本身之外，增加了其它字段   
- MVCC多版本并发控制中，读操作可以分为两类：  
  - 快照读：读的是记录的可见版本，不用加锁。如select  
  - 当前读：读取的是记录的最新版本，并且当前读返回的记录。   

# MySQL优化

**SQL和索引优化**

**应用层面优化**：除了优化SQL和索引，很多时候，在实际生产环境中，由于数据库服务器本身的性能局限，就必须要对上层的应用来进行一些优化，使得上层应用访问数据库的压力能够减到最小  

- **连接池**：应用上一般访问数据库，都是先和MySQL Server创建连接，然后发送SQL语句，Server处理完成后，再把结果通过网络返回给应用，然后关闭和MySQL Server的连接，因此短时间大量的数据库访问，消耗的TCP三次握手和四次挥手所花费的时间就很大了，稍微大一点的项目，我们都会在应用访问数据库的那一层上，添加连接池模块，相当于应用和MySQL Server事先创建一组连接，当应用需要请求MySQL Server时，不需要再进行TCP连接和释放连接了，一般连接池都会维护以下资源：  
  - 连接池里面保持固定数量的活跃TCP连接，供应用使用。  
  - 如果应用瞬间访问MySQL的量比较大，那么连接池会实时创建更多的连接给应用使用  
  - 当连接池里面的TCP连接一段时间内没有被用到，连接池会释放多余的连接资源，保留它设置的最大空闲连接量就可以了  
- **增加cache缓存：**业务上增加redis、memcache，一般用缓存把经常访问的数据缓存起来  

**MySQL Server优化：**（通过修改配置文件来启用一些功能）

- 开启MySQL 查询缓存
- 索引和数据缓存  
- MySQL线程缓存
- 设置并发连接数量和超时时间  

# MySQL日志

```mysql
show variables like 'log_%';  
```

错误日志：mysqld 使用错误日志名 host_name.err(host_name 为主机名) 并默认在参数 DATADIR(数据目录)指定
的目录中写入日志文件。  

查询日志：查询日志记录了客户端的所有语句。由于上线项目sql特别多，开启查询日志IO太多导致MySQL效率
低，只有在调试时才开启，比如通过查看sql发现热点数据进行缓存：`show global variables like "%genera%";  `

二进制日志：二进制日志(BINLOG)记录了所有的 DDL(数据定义语言)语句和 DML(数据操纵语言) 语句，但是不包括数据查询语句。语句以“事件”的形式保存，它描述了数据的更改过程。 此日志对于灾难时的数据恢复起着极其重要的作用  

- 应用场景：主从复制、数据恢复

- ```mysql
  # 查看binlog
  show binary logs;
  ```

慢查询日志：MySQL可以设置慢查询日志，当SQL执行的时间超过我们设定的时间，那么这些SQL就会被记录在慢查询日志当中，然后我们通过查看日志，用explain分析这些SQL的执行计划，来判定为什么效率低下，是没有使用到索引？还是索引本身创建的有问题？或者是索引使用到了，但是由于表的数据量太大，花费的时间就是很长，那么此时我们可以把表分成n个小表，比如订单表按年份分成多个小表等  

- ```mysql
  # 查看慢查询日志
  show variables like '%slow_query%';
  show variables like 'long%';
  ```

redo log 和 undo log：

- redo log：重做日志，用于记录事务操作的变化，确保事务的持久性。redo log是在事务开始后就开始记录，不管事务是否提交都会记录下来，在异常发生时（如数据持久化过程中掉电），InnoDB会使用redo log恢复到掉电前的时刻，保证数据的完整性。  
- InnoDB修改操作数据，不是直接修改磁盘上的数据，实际只是修改Buffer Pool中的数据。InnoDB总是先把Buffer Pool中的数据改变记录到redo log中，用来进行崩溃后的数据恢复。 优先记录redo log，然后再慢慢的将Buffer Pool中的脏数据刷新到磁盘上  
- undo log：回滚日志，保存了事务发生之前的数据的一个版本，用于事务执行时的回滚操作，同时也是实现多版本并发控制（MVCC）下读操作的关键技术。  

# MySQL集群

在实际生产环境中，如果对mysql数据库的读和写都在一台数据库服务器中操作，无论是在安全性、高可用性，还是高并发等各个方面都是不能满足实际需求的，一般要通过主从复制的方式来同步数据，再通过读写分离来提升数据库的并发负载能力。  

- 主从复制，热备份&容灾&高可用  
- 读写分离，支持更大的并发  

## 主从复制

<img align='left' src="img/MySQL.img/image-20210529155451429.png" alt="image-20210529155451429" style="zoom:50%;" />

主从复制的流程：两个日志（binlog二进制日志&relay log日志）和三个线程（master的一个线程和
slave的二个线程）  

- 主库的更新操作写入binlog二进制日志中  
- master服务器创建一个binlog转储线程，将二进制日志内容发送到从服务器  
- slave机器执行START SLAVE命令会在从服务器创建一个IO线程，接收master的binary log复制到其中继日志relay log  
  - 首先slave开始一个工作线程（I/O线程），I/O线程在master上打开一个普通的连接，然后开始binlog dump process，binlog dump process从master的二进制日志中读取事件，如果已经跟上master，它会睡眠并等待master产生新的事件，I/O线程将这些事件写入中继日志。  
- sql slave thread（sql从线程）处理该过程的最后一步，sql线程从中继日志中读取事件，并重放其中的事件而更新slave机器的数据，使其与master的数据一致。只要该线程与I/O线程保持一致，中继日志通常会位于os缓存中，所以中继日志的开销很小  

## 读写分离

读写分离就是在主服务器上修改，数据会同步到从服务器，从服务器只能提供读取数据，不能写入，实现备份的同时也实现了数据库性能的优化，以及提升了服务器安全。  

<img align='left' src="img/MySQL.img/image-20210529160156398.png" alt="image-20210529160156398" style="zoom:50%;" />

目前较为常见的MySQL读写分离方式有：  

- 程序代码内部实现  
- 引入中间代理层  
  - MySQL_proxy
  - Mycat

# MySQL分库分表

**数据库架构演变**

刚开始多数项目用单机数据库就够了，随着服务器流量越来越大，面对的请求也越来越多，我们做了数据库读写分离， 使用多个从库副本（Slave）负责读，使用主库（Master）负责写，master和slave通过主从复制实现数据同步更新，保持数据一致。slave 从库可以水平扩展，所以更多的读请求不成问题。  

但是当用户量级上升，写请求越来越多，怎么保证数据库的负载足够？增加一个Master是不能解决问题的， 因为数据要保存一致性，写操作需要2个master之间同步，相当于是重复了，而且架构设计更加复杂。  

这时需要用到分库分表（sharding），对写操作进行切分  

**库表问题**

- 单库太大：单库处理能力有限、所在服务器上的磁盘空间不足、遇到IO瓶颈，需要把单库切分成更多更小的库  
- 单表太大：CURD效率都很低、数据量太大导致索引膨胀、查询超时，需要把单表切分成多个数据集更小的表  
- 拆分策略：单个库太大，先考虑是表多还是数据多：  
  - 如果因为表多而造成数据过多，则使用垂直拆分，即根据业务拆分成不同的库  
  - 如果因为单张表的数据量太大，则使用水平拆分，即把表的数据按照某种规则拆分成多张表  
- 分库分表的原则应该是先考虑垂直拆分，再考虑水平拆分  

**垂直分表**

- 也就是“大表拆小表”，基于列字段进行
- 一般是表中的字段较多，将不常用的， 数据较大，长度较长（比如text类型字段）的拆分到“扩展表“。
- 一般是针对几百列的这种大表，也避免查询时，数据量太大造成的“跨页”问题。

**垂直分库**

- 垂直分库针对的是一个系统中的不同业务进行拆分  
- 比如用户User一个库，商品Product一个库，订单Order一个库， 切分后，要放在多个服务器上，而不是一个服务器上。
- 想象一下，一个购物网站对外提供服务，会有用户，商品，订单等的CRUD。没拆分之前， 全部都是落到单一的库上的，这会让数据库的单库处理能力成为瓶颈。按垂直分库后，如果还是放在一个数据库服务器上， 随着用户量增大，这会让单个数据库的处理能力成为瓶颈，还有单个服务器的磁盘空间，内存，tps等非常吃紧。
-  所以我们要拆分到多个服务器上，这样上面的问题都解决了，以后也不会面对单机资源问题 

**水平拆分**

将单张表的数据切分到多个服务器上去，每个服务器具有相应的库与表，只是表中数据集合不同。 水平分库分表能够有效的缓解单机和单库的性能瓶颈和压力，突破IO、连接数、硬件资源等的瓶颈  

<img align='left' src="img/MySQL.img/image-20210529161348990.png" alt="image-20210529161348990" style="zoom:50%;" />

# MySQL执行查询语句的过程

<img align='left' src="img/MySQL.img/image-20210529161636226.png" alt="image-20210529161636226" style="zoom:50%;" />

- 连接器：客户端先通过连接器连接到MySQL服务器
- 缓存：连接器权限验证通过之后，先查询是否有缓存，如果有缓存则直接返回缓存的数据，如果没有缓存则进入分析器
- 分析器：分析器会对查询语句进行语法分析和词法分析，判断SQL语句是否正确，如果查询语法错误会直接返回给客户端错误信息，如果语法正确则进入优化器
- 优化器：优化器是对查询语句进行优化处理，例如一个表里面有多个索引，优化器会判别哪个索引性能更好
- 执行器：优化器执行完就进入执行器，执行器就开始执行语句进入查询比对了，直到查询到满足条件的所有数据，然后进行返回

# 扩展：MySQL常用操作

```mysql
# 查看字符编码
show variables like 'charac%';

# 设置字符编码
set character_set_server=utf8;

# 查看存储引擎
show engines;  # show engines\G; \G 的作用是将查到的结果旋转90度变成纵向

# 修改已存在表的存储引擎
ALTER TABLE user ENGINE = InnoDB;

# 通过mysqldump命令把指定db里面的表进行备份，写入sql脚本中
mysqldump -h 9.84.205.186 -P 3306 -u root -p123456 db_name table_name >
data_log.sql

# 登录mysql后，通过source命令可以把sql脚本的数据恢复到db中：
source /home/localuser/data_log.sql

# 备份sql查询的数据
mysql -h 9.84.205.186 -P 3306 -u root -p123456 -D db_name -e "select * from
db_table" > data_log.txt

# 查看支持的用户连接
select Host,User,authentication_string from user;

# 设置支持ip远程连接
grant all privileges on *.* to 'root'@'%' identified by '123456' with
grant option;
flush privileges;
quit
[root@localhost Downloads]# service mysqld restart

# 通过ip远程连接mysql
mysql -h 172.20.10.3 -u root -p123456

# 创建账户，只能在本地访问
create user 'ms'@'localhost' identified by '123456';

# 创建账户，可以通过任意或指定ip访问
create user 'ms'@'%' identified by '123456';

# 新用户创建完成后，刷新库表赋予权限：
grant all privileges on *.* to 'tony'@'%' identified by '123456' with
grant option;
flush privileges;

/* 开放防火墙端口 */
# 开放3306端口
firewall-cmd --zone=public --add-port=3306/tcp --permanent
# 重启防火墙
firewall-cmd --reload
# 查看当前开放的端口列表
firewall-cmd --list-ports
```

# if函数

IF函数有时被称为IF ELSE或IF THEN ELSE函数。

IF函数语法如下：

```mhsql
IF(expr,if_true_expr,if_false_expr)
```

IF函数通常与SUM()函数组合，

```mysql
SELECT
    SUM(IF(status = 'Shipped', 1, 0)) AS Shipped,
    SUM(IF(status = 'Cancelled', 1, 0)) AS Cancelled
FROM
    orders;
```

IF函数与COUNT()函数组合，因为COUNT函数不计算空值，所以如果状态不在选定状态，IF函数将返回NULL，否则将返回1。

```mysql
SELECT
    COUNT(IF(status = 'Cancelled', 1, NULL)) Cancelled,
    COUNT(IF(status = 'Disputed', 1, NULL)) Disputed,
    COUNT(IF(status = 'In Process', 1, NULL)) 'In Process',
    COUNT(IF(status = 'On Hold', 1, NULL)) 'On Hold',
    COUNT(IF(status = 'Resolved', 1, NULL)) 'Resolved',
    COUNT(IF(status = 'Shipped', 1, NULL)) 'Shipped'
FROM
    orders;
```

