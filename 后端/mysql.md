#### 安装 MySQL

> linux 初次安装 mysql 会生成临时密码
> `grep 'temporary password' /var/log/mysqld.log` 查看临时密码

#### 数据类型

```SQL
----------------- number
tinyint -> 1Byte    smallint -> 2Byte
mediumint -> 3Byte  int/integer -> 4Byte
bigint -> 8Byte     float -> 4Byte
double -> 8Byte     decimal(n)
----------------- string
char(n) --定长，高性能
varchar(n) -- 变长，低性能
tinyblob mediumblob longblob
tinytext mediumtext longtext
----------------- datetime
year date time datetime timestamp
```

#### database

```SQL
-- 创建数据库base0
create database base0;
-- 创建数据库base0如果base0不存在
create database if not exists base0;
-- 创建数据库时指定字符集
create database base0 default charset utf8mb4;
-- 删除数据库base0
drop database base0;
-- 删除可能不存在的数据库base0
drop database if exists base0;
-- 显示当前所在数据库
select database()
-- 查询建库base0的语句
show create database base0;
```

#### table

```SQL
-- 建表table0 | AUTO_INCREMENT的第一个值是5 | 表的字符集是utf8mb4
create table table0 (
    <字段名> <type> AUTO_INCREMENT comment <字段的注释>, ...
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSEt=utf8mb4 comment <表的注释>;
-- 修改table0的表名
alter table table0 rename to <new_table_name>;
-- 查询所有表
show tables;
-- 查询表table0的结构
desc table0;
-- 查询table0的建表语句
show create table table0;
-- 删除并重新创建表
truncate table <table_name>;
-- 级联/限制删除表
drop table <table_name> cascade/restrict;
```

#### 字段

```SQL
-- 为table0添加字段
alter table table0 add <字段名> <type> comment <注释>;
-- 修改table0的字段的数据类型
alter table table0 midify <字段名> <type>;
-- 修改table0的某个字段
alter table table0 change <old_name> <new_name> <new_type> comment <注释>;
-- 删除table0的某个字段
alter table table0 drop <字段名>;
-- 部分字段添加数据
insert into <table_name> <filed1, field2, ...> values (value1, value2, ...);
-- 所有字段添加数据
insert into <table_name> values (value1, value2, ...);
-- 插入一张表的子查询结果，注意列的匹配
insert into table_name select(...)
-- 查看表中所有数据
select * from <table_name>;
-- 修改某字段所有数据
update <table_name> set <field>=<value>;
-- 根据条件修改数据
update <table_name> set <field>=<value> where <field0>=<value0>;
-- 清空表
delete from <table_name>;
-- 根据条件删除数据
delete from <table_name> where <field>=<value>;
```

#### 索引

```SQL
---------------- 建立索引 -----------------
-- 在user表中的id字段建立名为index_name的UNIQUE索引
-- UNIQUE：此索引的每一个索引值只对应唯一的数据记录
CREATE UNIQUE INDEX idx_name ON user(id DESC);
```

#### 聚合函数

```SQL
count() -- 统计个数
avg() -- 统计平均值
max()/min() -- 求最大/最小值
----------- string -----------
concat(<strA>, <strB>) -- 字符串拼接
lower(<str>) -- 字符串转小写
upper(<str>) -- 字符串转大写
lpad(<str>, <num>, <placeholder>) -- 左填充
rpad(<str>, <num>, <placeholder>) -- 右填充
trim(<str>) -- trim
subString(<str>, <from>, <to>) -- 放回指定位置的子串
----------- number -----------
ceil(<num>)  floor(<num>)  mod(<A>, <B>)
rand() -- 产生一个[0-1)的随机数
round(3.14159265, 2) -- 3.14
----------- Date/Time -----------
curdate()  curtime() now()
year(<date>) month(<date>) day(<date>)
date_add(<date>, interval <num> day) -- 日期天数计算
datediff(<date1>, <date2>) -- date1与date2间隔天数
----------- Process Control -----------
if(<bool>, 'ok', 'no') -- 流程控制
ifnull(null, 'default') -- 应对null数据的流程控制
```

#### 视图

```SQL
---------- 建立视图 ----------
-- select语句不会执行，查询视图时，才会按视图的定义从基本表中拉取数据
CREATE view 视图名 (用户名, 密码)
AS SELECT username, password FROM user
WITH CHECK OPTION;
---------- 删除视图 ----------
DROP VIEW 视图名 CASCADE; -- 级联删除
```

#### 其他

```SQL
where <field> like '__'; -- 匹配字段field只有两位字符的
where <filed> like '%x%'; -- 表示x前后不知道有什么，包括个数
rlike -- 正则匹配
-- 检索前num行
select * from <table_name> limit <num>;
-- 检索从第A行开始，每页B行数据
select * from <table_name> limit <A>, <B>;
-- case ... when ... end 语句
select case <field> when <condition> then 'if_ture' else 'if_false' end;
select distinct <field> from <table_name>; -- 对field列去重
where <X> in ('selection1', 'selection2', 'selection3'); -- X在某个范围
---------- 集合操作 ----------
-- 参加集合操作的各表列数必须相同，对应数据项的数据类型也必须相同
UNION -- 并操作，可以拼接两个select的结果
INTERSECT -- MYSQL不支持，使用 INNER JOIN 替代
EXCEPT -- MYSQL不支持，使用 LEFT JOIN 替代
A full outer join B -- 取并集，彼此没有对应的值为null
-- union和or是去重的，union all不去重
-- where适用于分组前，having是在GROUP BY的结果之上做筛选
-- ANY、SOME、ALL匹配子查询
ANY(SELECT * from t)
-- 带有(NOT) EXISTS谓词的子查询，EXISTS返回的是布尔值
WHERE NOT EXISTS (SELECT * from t WHERE ...)
---------- 派生表
SELECT * from (SELECT a from ...) AS t ...
---------- NULL的运算
-- NULL与另一个值的算术运算结果为NULL
-- NULL与另一个值的逻辑运算结果为UNKNOWN

```

#### 用户与权限

```SQL
-- 查询所有用户
select * from mysql.user;
-- 创建任意访问用户
create user '<user_name>'@'%' identified by '<password>';
-- 创建本地访问用户
create user '<user_name>'@'localhost' identified by '<password>';
-- 使用native密码创建用户
create user '<user_name>'@'%' identified with mysql_native_password by '<password>';
-- 删除用户
drop user '<user_name>'@'localhost';
-- ********** 权限控制 **********
-- 查询权限
show grants for '<user_name>'@'localhost';
-- 授权
grant all on <database_name.table_name> to '<user_name>'@'localhost';
-- revoke power
revoke all on <database_name.table_name> from '<user_name>'@'localhost';
```

#### 约束

```SQL
create table user(
    id int unsigned primary key auto_increment,
    name varchar(10) not null unique,
    age int check (age > 0 and age < 100),
    status char(1) default '0',
    -- 如果多个属性为主键，应当使用表级约束
    CONSTRAINT user PRIMARY KEY (id, name),
    -- 外键
    CONSTARINT user FOREIGN KEY (status) REFERENCES other_db.other_t(status),
    -- 级联删除（更新）tablex表中相应的元组
    FOREIGN KEY (status) REFERENCES tablex(id) ON DELETE(UPDATE) CASCADE,
    -- 定义表级约束
    CHECK (name NOT LIKE '账号已注销')
)
```

#### 事务

```SQL
-- 事务：一组操作的集合，要么同时成功，要么同时失败
-- 事务一旦提交或回滚，对数据库内的数据的改变就是永久的
-- 查看是否自动提交事务
select @@autocommit;
-- 设置自动提交事务
set @@autocommit = 1;
-- 开启事务，两种方式
start transaction;
begin;
-- 提交事务
commit;
-- 回滚事务
rollback;
```

#### mysql 存储引擎

- 存储引擎是基于表的，而不是基于库的，存储引擎也被称为 `表类型`
- mysql5.5以后默认的存储引擎是 InnoDB
- 查询当前数据库支持的存储引擎
- MEMORY的数据存储在内存，通常做临时表及缓存
- 查看所有支持的引擎 `SHOW ENGINES;`

#### InnoDB

- 支持 `事务`
- 支持 `行级锁`，提高访问性能
- 支持 `外键`
- 默认每张表都对应一个磁盘文件 `<表名>.ibd`
- 查询是否每一张表对应一个磁盘文件
    `show variables like 'innodb_file_per_table';`
- Windows下数据的默认存储位置
    `C:\ProgramData\MySQL\MySQL Server 8.0\Data\<数据库名>\<表名.ibd>`
- 使用查看表结构（将以 json 格式输出 ibd）
    `ibd2sdi <表名.ibd>`
- 逻辑存储结构
    - TableSpace(表空间)
        - ibd 文件就是表空间文件
        - TableSpace 包含若干 Segment
    - Segment(段)
        - Segment 包含若干 Extent
    - Extent(区)
        - Extent 包含64个 Page
        - 大小为 1M
    - Page(页)
        - Page 包含若干 Row
        - 大小为 16K
    - Row(行)
        - Row 包含
            - Trx id(最后一次操作的事务ID)
            - Roll Pointer(指针)
            - 一个一个的字段

#### MyISAM

- MyISAM 是 mysql 的早期存储引擎
- 不支持事务
- 不支持外键
- 支持表锁，不支持支行锁
- 访问速度快
- sdi文件存放表结构
    - sdi 可以直接打开，使用 JSON 格式
- MYD 文件存放数据
- MYI 文件存放索引

#### Memory

- 使用内存存放数据
- 默认使用 Hash 索引
- sdi 文件存放表结构

![image.png](attachments/image.png)

#### mysql 索引

- 索引是 `有序` 的数据结构
- 无索引查询数据只能全表扫描，性能极差
