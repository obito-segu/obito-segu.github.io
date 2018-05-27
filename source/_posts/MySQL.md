---
title: MySQL
date: 2018-04-19 19:47:03
tags:
categories:
	- Computer Science
	- Database
---
```
# 创建数据库
CREATE {DATABASE | SCHEMA} [IF NOT EXISTS] db_name
[[DEFAULT] CHARACTER SET [=] charset_name]；


# 修改数据库
ALTER {DATABASE | SCHEMA} [db_name]
[DEFAULT] CHARACTER SET [=] charset_name;

# 删除数据库
DROP {DATABASE | SCHEMA} [IF EXISTS] db_name;

# 查看数据库字符集编码
SHOW CREATE DATABASE db_name;

```



```
# 查看数据库列表
SHOW TABLES [FROM db_name]
[LIKE 'pattern' | WHERE expr];


# 查看数据表结构
SHOW COLUMNS FROM tbl_name


# 插入记录
INSERT [INTO] tbl_name [(col_name,...)] VALUE(val,...)

# 查找记录
SELECT expr,... FROM tbl_name

# 自动编号
"""
AUTO_INCREMENT
必须和主键组合使用
默认：1，2，3，。。。
"""




"""
1、查看版本号：select version();
2、查看日期：select now();
3、查看用户：select user();
""" 
```



```
# 外键约束的要求
# 1.父表和子表使用相同的数据引擎；禁止使用临时表
# 2.数据表的存储引擎为InnoDB
# 3.外键列和参照列有相似的数据类型。数字的长度和符号位必须相同；字符则可以不同
# 4.外键列和参照列必须创建索引。外键列不存在索引则自动创建
create table provience (
id smallint unsigned auto_increment primary key,
proname varchar(20) not null
);

create table user(
id smallint unsigned auto_incremene primary key,
username varchar(10) not null,
pid smallint unsigned 
foreign key (pid) references provience(id) on delete cascade(set null, restrict, not action)
);


delete from tb_name where id=?;父表和子表一对一才行，如果对应多个子表将会删除失败

```




```
# 添加单列
ALTER TABLE tbl_name ADD [COLUMN] col_name column_definition [FIRST | AFTER col_name]
# 添加多列
ALTER TABLE tbl_name ADD [COLUMN](col_name column_definition,...)

# 删除列
ALTER TABLE tbl_name DROP [COLUMN] col_name

# 添加主键约束
ALTER TABLE tbl_name ADD [CONSTRAINT [symbol]]
PRIMARY KEY [index_type](index_col_name,...)

# 添加唯一约束
ALTER TABLE tbl_name ADD [CONSTRAINT [symbol]]
UNIQUE [INDEX|KEY] [index_name] [index_type]
(index_col_name,...)

# 添加外键约束
ALTER TABLE table_name ADD CONSTRAINT fk_column_id FOREIGN KEY[COLUMN]references (column_id);

# 添加/删除默认约束
ALTER TABLE tbl_name ALTER [COLUMN] col_name {SET DEFAULT literal | DROP DEFAULT}

# 删除主键约束
ALTER TABLE tbl_name DROP PRIMARY KEY

# 删除唯一约束
ALTER TABLE tbl_name DROP {INDEX|KEY} index_name

# 删除外键约束
ALTER TABLE tbl_name DROP FOREIGN KEY fk_symbol

# 修改列定义
ALTER TABLE tbl_name MODIFY [COLUMN] col_name column_definition  [FIRST|AFTER col_name]

# 修改列名称
ALTER TABLE tbl_name CHANGE [COLUMN] old_col_name new_col_name column_definition  [FIRST|AFTER col_name]

# 修改数据表名称
ALTER TABLE 表名 RENAME 新表名；
```



```
# 插入记录
INSERT [INTO] tbl_name [(col_name,...)] {VALUES|VALUE}
({expr|DEFAULT},...),(...),...

INSERT [INTO] tbl_name SET col_name={expr|DEFAULT},...


# 更新记录
UPDATE [LOW_PRIORITY] [IGNORE] table_reference SET col_name1={expr1|DEFAULT},[,col_name2={expr2|DEFAULT}]... [WHERE where_condition]


# 删除记录
DELETE FROM tbl_name [WHERE where_condition]

# 查找记录
SELECT select_expr [,select_expr ...]
[
	FROM table_references
	[WHERE where_condition]
	[GROUP BY {col_name|position} [ASC|DESC],...]
	[HAVING where_condition]
	[ORDER BY {col_name|expr|position}[ASC|DESC],...]
	[LIMIT {[offset,] row_count | row_count OFFSET offset}]
]

# 查询结果分组
[GRPOUP BY {col_name|position} [ASC|DESC],...]
```


```
NOW() //当前时间 含日期时间
CURDATE() //当前日期 只有日期
CURTIME() //当前时间 只有时间
DATE_ADD() //时间增减或减少
SELECT DATE_ADD('2014-3-12',INTERVAL 365 DAY); ==>2015-3-12 //在原有给定的时间上增加365天
INTERVAL增加可以增加负值 单位 year ,month,week,day
SELECT DATEDIFF('2014-1-1','2015-1-1') ==> -365 //时间差值计算 单位为日 前面时间减去后面时间
SELECT DATE_FORMAT('2014-3-2','%m/%d/%d'); ==> 03/02/2014 //日期格式转换
```



```
# 创建函数
CREATE FUNCTION function_name
RETURNS
{STRING|INTEGER|REAL|DECIMAL}
routine_body

# 创建带有参数的自定义函
CREATE FUNCTION f2(num1 SMALLINT UNSIGNED,num2 SMALLINT UNSIGNED
RETURNS FLOAT(10,2) UNSIGNED
RETURN (num1+num2)/2;


# BEGIN 和 END 之间，可以执行多条语句
```



```
# 创建存储过程
CREATE
[DEFINER = {user|CURRENT_USER}]
PROCEDURE sp_name ([proc_parameter[,...]])
[characteristic ...] routine_body

proc_parameter:
[IN|OUT|INOUT] param_name type


创建存储过程
语法格式：CREATE PROCEDURE 存储过程名 （[参数],[参数]...）
例如：CREATE PROCEDURE sq1() SELECT VERSION();
调用存储过程
CALL 存储过程名[(参数)]，...
例如：CALL sp1();无参可以省略小括号。有参就不能省略

创建参数为IN类型的存储过程，在对数据表执行某些操作时参数名和数据表字段不可一致，修改过程体时需删除重建过程<br>
DELIMITER //
CREATE PROCEDURE removeTableByid(INT p_id unsigned)
BEGIN
DELETE FROM table_name where id=p_id;
END//



# 创建带有IN类型参数的正确写法
DELIMITER //
CREATE PROCEDURE removeUserById(IN p_id INT UNSIGNED)
BEGIN
DELETE FROM uses WHERE id = p_id;
END
//



# 创建带有IN和OUT类型参数的储存过程
DELIMITER //
CREATE PROCEDURE removeUserAndReturnUserNums(IN p_id INT UNSIGNED,OUT userNums INT UNSIGNED)
BEGIN
DELETE FROM users WHERE id = p_id;
SELECT count(id) FROM users INTO userNums;
END
//


# 多个out参数的存储过程
DELIMITER //
CREATE PROCEDURE removeUserByAgeAndReturnInfos(IN p_age SMALLINT UNSIGNED,OUT deleteUser SMALLINT UNSIGNED,OUT userCounts SMALLINT UNSIGNED)
BEGIN
DELETE FROM users WHERE age = p_age;
SELECT ROW_COUNT(id) INTO deleteUsers;
SELECT COUNT(id) FROM users INTO userCounts;
END
//


# 多个out参数存储过程调用
CALL removeUserByAgeAndReturnInfos(23,@a,@b);
SELECT @a,@b;

```



