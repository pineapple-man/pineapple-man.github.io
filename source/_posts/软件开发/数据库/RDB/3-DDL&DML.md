---
title: MySQL(三)DDL、DML
toc: true
clearReading: true
thumbnailImage: "https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/mysql/mysql.jpg"
thumbnailImagePosition: right
metaAlignment: center
categories: 数据库
tags: MySQL
keywords: mysql
excerpt: mysql DQL 数据查询语言，用来查询记录是学习 SQL 的重点之一
date: 2022-01-30 15:18:46
---

<!-- toc -->

## 数据类型

:notes: 数据类型选取的三大原则:
{% alert success no-icon %}

1. 选取的类型，越准确越好，尽量使用可以正确存储数据的最小数据类型
2. 类型越简单越好，因为简单数据类型的操作通常需要更少的 CPU 周期
3. 尽量避免 NULL ，如果查询中包含可为 NULL 的列，对 MySQ L 来说更难优化，这是由于为 NULL 的列增加 、索引统计和值比较都更复杂

{% endalert %}

数据类型主要分为以下四种：**数值类型**、**字符类型**、**日期类型**和**二进制类型**

### 数值类型

数值型主要包括三个部分：整型、小数和位类型

#### 整数类型

|     整数类型     | 字节 |                                             范围                                              |
| :--------------: | :--: | :-------------------------------------------------------------------------------------------: |
|   **Tinyint**    |  1   |                                有符号：-128~127 无符号：0~255                                 |
|   **Smallint**   |  2   |                             有符号：-32768~32767 无符号：0~65535                              |
|    Mediumint     |  3   |                          有符号：-8388608~8388607 无符号：0~1677215                           |
| **Int**、integer |  4   |                     有符号：- 2147483648~2147483647 无符号：0~4294967295                      |
|    **Bigint**    |  8   | 有符号： -9223372036854775808 ~9223372036854775807 <br /> 无符号：0~ 9223372036854775807\*2+1 |

:notes:整型数据类型相关注意点
{% alert success no-icon %}

1. 如果不指定整型是无符号还是有符号，则默认是有符号。可以手动田间`UNSIGNED`指定类型位无符号整型
2. 如果存入的数值超出类型范围，会发生`out of range`错误提示
3. MySQL 支持在类型名称后面的小括号内指定显示宽度，如果数字位数足够或大于，则忽略宽度，如果数字位数不够的空间用字符`0`填满，但要配合`ZEROFILL`使用，不手动指定宽度，使用默认宽度
4. `AUTO_INCREMENT`属性，该属性只能用于整型，AUTO_INCREMENT 标识列一般从 1 开始，每行增加 1，不用手动插入，但必须定位此列位`PRIMARY_KEY`或`UNIQUE`键

{% endalert %}

```sql
CREATE TABLE IF NOT EXISTS myTab(
	C1 TINYINT ZEROFILL,
	C2 SMALLINT ZEROFILL,
	C3 MEDIUMINT ZEROFILL,
	C4 INT ZEROFILL,
	C5 BIGINT ZEROFILL,
	C6 TINYINT UNSIGNED ZEROFILL,
	C7 SMALLINT UNSIGNED ZEROFILL,
	C3 MEDIUMINT UNSIGNED ZEROFILL,
	C4 INT UNSIGNED ZEROFILL,
	C5 BIGINT UNSIGNED ZEROFILL,
);	#最终查看表结构信息,发现所有类型自动变为无符号
```

#### 小数类型

MySQL 中小数类型共分为两种：**浮点数**和**定点数**

|          浮点数类型           |   字节   |                                范围                                 |
| :---------------------------: | :------: | :-----------------------------------------------------------------: |
|           **float**           |  **4**   |                  ±1.75494351E-38~±3.402823466E+38                   |
|          **double**           |  **8**   |         ±2.2250738585072014E-308~ ±1.7976931348623157E+308          |
|        **定点数类型**         | **字节** |                              **范围**                               |
| **DEC(M,D)** **DECIMAL(M,D)** | **M+2**  | 最大取值范围与 double 相同，给定 decimal 的有效取值范围由 M 和 D 定 |

:notes: 小数类型注意事项
{% alert info no-icon %}

浮点数和定点数都可以用类型名称后加`(M,D)`的方式来表示。其中 `M` 表示精度，该值的整数位+小数位一共显示 `M` 位数字；D 表示标度，小数位数一共显示 D 位数，超出则四舍五入，不够补零

{% endalert %}

具体的例子如下：

{% alert warning no-icon %}

```sql
CREATE TABLE IF NOT EXISTS myTab(
	F1 FLOAT(5,3),
	F2 DOUBLE(5,3),
	F3 DECIMAL(5,3)
);
```

{% endalert %}

:vs: 浮点数和定点数的区别：
{% alert success no-icon %}

1. <font style="color:red;font-weight:bold">定点数在 MySQL 内部以字符串形式存放</font>，比浮点数更精确，适合用于表示货币等**精度高的数据**
2. 在不指定精度时，浮点数默认会按照实际的精度来显示，而<font style="color:red;font-weight:bold">定点数在不指定精度时，默认为(10,0)</font>
3. **浮点数**如果数据<font style="color:red;font-weight:bold">越过了精度和标度值</font>，则<font style="color:red;font-weight:bold">自动将四舍五入后的结果插入</font>，系统不会报错；<font style="color:red;font-weight:bold">定点数将会报错</font>

{% endalert %}

#### 位类型

|   位类型   | 字节 |     范围      |
| :--------: | :--: | :-----------: |
| **Bit(M)** | 1~8  | Bit(1)~bit(8) |

:notes: 位类型注意事项
{% alert success no-icon %}

1. 位类型会将数值转换位二进制数存入数据库中
2. M 的范围是:`1~64`，表示位类型需要的二进制位数，如果超过，会报`Out of range`错误，不写默认为`1`
3. 一般搭配 `BIN()` 显示为二进制格式或 `HEX()` 显示为十六进制格式两个函数进行读取

{% endalert %}

```mysql
CREATE TABLE IF NOT EXISTS myTab(
	b1 BIT,
	b2 BIT(8)
);
```

### 字符类型

:notes: 用来保存 MySQL 中较短的字符串

| 字符串类型 | 最多字符数 |      描述及存储需求      |
| :--------: | :--------: | :----------------------: |
|  char(M)   |     M      |  M 为 0~255 之间的整数   |
| varchar(M) |     M      | M 为 0~65535 之间的整数  |
|    text    |    :x:     | 字符串类型，存储较长文本 |

:vs: `char` 与 `varchar` 类型对比
{% alert success no-icon %}

1. 存储的列长度
   - CHAR：创建表时声明列的长度且长度固定
   - VARCHAR：创建时声明列的最大长度
2. 数据检索时
   - CHAR：删除了尾部的空格
   - VARCHAR：保留了这些空格
3. 效率对比
   - CHAR 长度固定，效率高于 VARCHAR

{% endalert %}

```mysql
CREATE TABLE IF NOT EXISTS myTab(
	id INT NOT NULL PRIMARY KEY AUTO_INCREMENT,
    n1 VARCHAR(4),
    n2 CHAR(4)
);
```

对于字符串类型，常用的就是 `char` 以及 `varchar` ；为了使用的完整性，下面介绍不经常使用的字符类型

|   类型    |                                                                               含义                                                                                |
| :-------: | :---------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|  BINARY   |                                                               类似于 CHAR,不同的是它包含二进制字符                                                                |
| VARBINARY |                                                           类似于 VARCHAR,不同的是它包含可变二进制字符串                                                           |
|   Enum    | 枚举类型，要求插入的值必须属于列表中指定的值之一，如果列表成员为 1~255，则需要 1 个字节存储，如果列表成员为 255~65535，则需要 2 个字节存储；最多存储 65535 个成员 |
|    SET    |                      和 Enum 类型类似，里面可以保存 0~64 个成员。和 Enum 类型最大的区别是：SET 类型一次可以选取多个成员，而 Enum 只能选一个                       |

binary 和 varbinary 也可以用于存储字符型，不同的是，binary(M)和 varbinary(M):M 代表的是最多字节数

:sparkles: Enum 类型特点：
{% alert success no-icon %}

1. 必须用单引号引起来
2. 类型的值忽略大小写
3. 将一个无效值插入一个 ENUM (即，一个不在允许值列表中的字符串)，空字符串将作为一个特殊的错误值被插入。事实上，这个字符串有别于一个「 普通的 」空字符串，因为这个字符串有个数字索引值为 0
4. 成员值的索引从 1 开始，所以可以通过插入索引值的方式插入值，会自动显示为对应的成员值
5. Enum 类型的值必须为固定的值，不能是函数、表达式、变量
6. 对于允许插入 null 的 Enum 类型，可以插入 null 值，序列号默认为 null

{% endalert %}

SET 类型根据成员个数不同，存储所占的字节也不同

|  成员数  | 字节数 |
| :------: | :----: |
|  1 ～ 8  |   1    |
| 9 ～ 16  |   2    |
| 17 ～ 24 |   3    |
| 25 ～ 32 |   4    |
| 33 ～ 64 |   8    |

:notes: 真正 `SET` 类型存储的大小，根据版本不同可能有所不同

### 日期类型

|    日期和时间类型    | 字节 |       最小值        |       最大值        |
| :------------------: | :--: | :-----------------: | :-----------------: |
|   DATE(yyyy-MM-dd)   |  4   |     1000-01-01      |     9999-12-31      |
| DATETIME(日期+时间)  |  8   | 1000-01-01 00:00:00 | 9999-12-31 23:59:59 |
| TIMESTAMP(日期+时间) |  4   |   199700101080001   |  2038 年的某个时刻  |
|    TIME(hh:mm:ss)    |  3   |     -838:59:59      |      838:59:59      |
|         YEAR         |  1   |        1901         |        2155         |

:vs: `DATETIME`与`TIMESTAMP`的区别

{% alert success no-icon %}

1. `TIMESTAMP`支持的时间范围较小；`DATETIME`支持的时间范围非常大，但使用的字节多
2. `TIMESTAMP`和实际时区有关，更能反映实际的日期，而`DATETIME`则只能反映出插入时的当地时区
3. `TIMESTAMP`的属性受 MySQL 版本和 SQLMode 的影响很大

{% endalert %}

### 二进制类型

二进制类型常用的只有 BLOB 类型，BLOB 类型经常用于存储图片数据

## DDL 语言

DDL(Data Define Language)数据定义语言，用于对数据库和表的管理和操作，对应的基本操作如下表：

|               DDL 语句               |                      含义                      |
| :----------------------------------: | :--------------------------------------------: |
|           SHOW DATABASES;            |                   查看数据库                   |
|              USE mysql;              |               使用 mysql 数据库                |
|       SHOW CREATE TABLE 表名;        |              查看指定表的创建语句              |
|             SHOW TABLES;             |        展示 mysql 数据库中的所有数据表         |
| SHOW TABLES FROM information_schema; | 不改变当前所在的数据库，查看相应数据库中的表格 |

按照对数据库操作的主体不同，DDL 语言主要可以分为以下两部分：**创建数据库**、**删除数据库**

### 数据库定义操作

对于数据库的操作，通常使用的只有两种操作：创建数据以及删除数据库

{% alert warning no-icon %}

创建数据库的语句如下：

{% endalert %}

```sql
CREATE DATABASE databaseName；	#多次运行会报错
CREATE DATABASE [IF NOT EXISTS] databaseName;
```

{% alert warning no-icon %}

删除数据库的语句如下：

{% endalert %}

```sql
DROP DATABASE databaseName;	#没有进行表检查
DROP DATABASE IF EXISTS databaseName;
```

### 对数据库中的表进行操作

选择了对应的数据库后，就可以对数据库中存在的表进行操作，对表的操作有以下几种：创建表、删除表和修改表

#### 创建表

表的创建拥有两种方式：**自定义表结构**以及**使用子查询创建表**

##### 自定义表结构

```sql
CREATE TABLE [IF NOT EXISTS] 表名(
	字段名 数据类型 [字段约束],
	字段名 数据类型 [字段约束],
	字段名 数据类型 [字段约束],
	字段名 数据类型 [字段约束]
);
```

:notes:命名规则

{% alert success no-icon %}

1. 数据库名不得超过 30 个字符，变量名限制为 29 个
2. 必须只能包含 A–Z, a–z, 0–9, \_共 63 个字符
3. 不能在对象名的字符间留空格
4. 必须不能和用户定义的其他对象重名
5. 必须保证你的字段没有和保留字、数据库系统或常用方法冲突
6. 保持字段名和类型的一致性,在命名字段并为其指定数据类型的时候一定要保证一致性。假如数据类型在一个表里是整数,那在另一个表里可就别变成字符型了

{% endalert %}

{% alert warning no-icon %}

创建 stus 表,要求具有学生学号、姓名、年龄、性别属性

```sql
CREATE TABLE stus(
	sid	    CHAR(6),
	sname	VARCHAR(20),
	age		INT,
	gender	VARCHAR(10)
);
```

{% endalert %}

##### 使用子查询创建表

```sql
CREATE TABLE 表名[(column,column,...,column)]
AS 子查询
```

:notes: 使用子查询创建表注意点：

{% alert success no-icon %}

1. 子查询的结果列应当和定义中表的列相互对应
2. 通过列名和默认值定义列

{% endalert %}

:older_man: 开发中常用如下方式创建表，这是因为这种方式能够保证创建表的确定性

```sql
DELETE TABLE IF EXISTS 需要创建的表名;
CREATE TABLE 需要创建的表名;
```

#### 删除表

```sql
DROP TABLE [IF EXISTS ] tableName;
```

#### 修改表

对表的修改体现在对表的列进行修改，主要完成以下几点功能：**修改表名**、**增加属性列**、**修改属性列名**、**修改属性列的数据类型**、**修改属性列的完整性约束**、**删除属性列**

修改表的基本语句如下：

```sql
ALTER TABLE 表名 ADD|MODIFY|DROP|CHANGE COLUMN 字段名 [字段类型];
```

:vs:`CHANGE`与`MODIFY`的区别
{% alert success no-icon %}

- CHANGE:主要操作字段名的修改，同样会进行字段间数据类型的更改，对于字段间的完整性约束并不会进行修改
- MODIFY:主要修改字段类型以及完整性约束

{% endalert %}

##### 修改字段名

```sql
ALTER TABLE CHANGE COLUMN 旧字段名 新字段名 新字段数据类型 新字段约束;
```

{% alert warning no-icon %}

修改 stu 表的 gender 列名为 sex

```sql
ALTER TABLE stu CHANGE COLUMN gender sex CHAR(2);
```

{% endalert %}

##### 修改表名

```sql
ALTER TABLE 旧表名 RENAME TO 新表名;
#修改stu表名称为student
ALTER TABLE stu RENAME [TO] student;
```

##### 修改字段类型和列级约束

```sql
ALTER TABLE 表名 MODIFY COLUMN 字段名 新字段数据类型;
```

{% alert warning no-icon %}

修改 stu 表的 gender 列类型为 CHAR(2)

```sql
ALTER TABLE stu MODIFY COLUMN gender char(2);
```

{% endalert %}

##### 添加字段

```sql
ALTER TABLE 表名 ADD COLUMN 新字段名 新字段类型 [新字段约束];
```

给 stus 表添加 classname 列

```sql
ALTER TABLE stu ADD COLUMN classname char(10);
```

##### 删除字段

```sql
ALTER TABLE 表名 DROP COLUMN 列名;
#删除stu表的classname列
ALTER TABLE stu DROP COLUMN classname;
```

## DML 语言

DML(Data Manipulation Language) 数据操作语言，用来定义数据库记录(数据)

### 向表中插入数据(增)

向表中插入数据的基本格式如下：

```sql
INSERT INTO 表名 (字段名1,字段名2,...,字段名n)
VALUES (值1,值2,...,值n),(值1,值2,...,值n),...,(值1,值2,...,值n)
```

:sparkles: `INSERT` 语句特点
{% alert success no-icon %}

1. 字段类型和值类型一致或兼容，而且一一对应(包括类型、约束等匹配)
2. 可以为空的字段(NULL 约束)，可以不用插入值，或用 NULL 填充
3. 不可以为空的字段(NOT NULL 约束)，必须插入值
4. 字段个数和值的个数必须是一致
5. 字段可以省略，但默认所有字段，并且顺序和表中的存储顺序一致
6. 数值型的值，不用单引号；非数值型的值，必须使用单引号

{% endalert %}

```sql
# 将所有字段都书写
insert into stu (sid,sname,age,sex) values
(1,"a",11,'m'),(2,"a",11,'m'),(3,"a",11,'m');

# 省略字段名，表示按创建表时列的顺序插入所有列的值
insert into stu values
(1,"a",11,'m'),(2,"a",11,'m'),(3,"a",11,'m');

## 只给某些特定字段插入数据，其他字段使用默认值或NULL
INSERT INTO stu (sname,sex) VALUES
("c",'m');
```

{% alert info no-icon %}

:notes: 所有字符串数据必须使用单引号

{% endalert %}

### 修改表中数据(改)

修改表中数据分为：修改单表数据和修改多表数据

#### 修改单表数据

```sql
UPDATE 表名 SET 字段=新值,字段=新值
[WHERE 筛选条件]
```

{% alert warning no-icon %}

```sql
#此操作将修改所有记录
update stu set sid='123456',sname="张三",age=18,sex='m';

#修改满足特定条件的行
update stu set sid='123456',sname="李四",age=18,sex='f' where sid is null;
```

{% endalert %}

#### 修改多表数据

修改多表数据类似于多表连接操作 + 更新操作，具体的使用格式如下：

```sql
## SQL 92
UPDATE 表1 别名1,表2 别名2
SET 字段=新值,字段=新值
WHERE 连接条件 AND 筛选条件

## SQL 99
UPDATE 表1 [AS] 别名1 [INNER] JOIN 表2 [AS] 别名2 ON 连接条件
SET 字段=新值,字段=新值
WHERE 筛选条件
```

{% alert warning no-icon %}

```sql
## SQL 92语法进行多表连接
update stu as s1, stu2 as s2
set s1.`sname`='王五',s2.`sname`='赵六'
where s1.`sid`=s2.`sid` and s1.`sex`='f';

#SQL 99语法进行多表连接
update stu as s1 inner join stu2 s2
on s1.`sid`=s2.`sid`
set s1.`sname`='三张',s2.`age`=25
where s1.`sname`='张三';
```

{% endalert %}

### 删除表中数据(删)

删除表中数据分为：单表的删除和多表的删除

#### 单表的删除

```sql
## 方式一:使用DELETE语句
DELETE FROM 表名
[WHERE 筛选条件]

## 方式二：使用TRUNCATE语句
TRUNCATE TABLE 表名
```

{% alert warning no-icon %}

```sql
## 删除stu表中名字为王五的记录
delete FROM stu where sname='王五';

#删除stu2表中的所有记录
truncate table stu2;
```

{% endalert %}

#### 多表的删除

```sql
DELETE 别名1,别名2
FROM 表1 别名1,表2 别名2
WHERE 连接条件 AND 筛选条件
```

:vs: `DELETE` 语句与 `TRUNCATE` 语句的区别

:notes: 两种方式删除表的区别如下：
{% alert success no-icon %}

1. delete 可以添加 WHERE 条件，TRUNCATE 不能添加 WHERE 条件，一次性清除所有数据
2. TRUNCATE 效率高于 DELETE
3. 使用 DELETE 删除后，重新插入数据，记录从断点处开始
   使用 TRUNCATE 删除后，重新插入数据，记录从 1 开始
4. TRUNCATE 删除后事务不能回滚，DELETE 删除后可以回滚
5. delete 删除数据，会返回受影响的行数；TRUNCATE 删除数据，不返回受影响的行数

{% endalert %}

:notes: TRUNCATE 实际操作是将表中所有数据删除，而后创建了一个具有相同表结构的新表

## 完整性约束

{% alert success no-icon %}

为了保证数据的一致性和完整性，SQL 规范以约束的方式对表数据进行额外的条件限制。其中完整性约束是为了表的数据的正确性！如果数据不正确，那么一开始就不能添加到表中。简而言之，约束是表级的强制规定

{% endalert %}

可以在创建表时规定约束（通过 CREATE TABLE 语句），或者在表创建之后也可以（通过 ALTER TABLE 语句）

### 约束分类

常用的六种约束及其对应的含义如下：

| 完整性约束  |                   含义                   |
| :---------: | :--------------------------------------: |
|  NOT NULL   |                 非空约束                 |
|   DEFAULT   |                  默认值                  |
|   UNIQUE    | 唯一约束，规定某个字段在整个表中是唯一的 |
|    CHECK    |                 检查约束                 |
| PRIMARY KEY |           主键约束(非空且唯一)           |
| FOREIGN KEY |                 外键约束                 |

:notes:MySQL 不支持 check 约束，但可以使用 check 约束，就是任何效果

根据**约束所要求的列数**，可以将约束类型分为以下两种:单列约束和多列约束

| 约束类型 |          含义          |
| :------: | :--------------------: |
| 单列约束 |   每个约束只约束一列   |
| 多列约束 | 每个约束可约束多列数据 |

根据约束的**作用范围**，约束可分为：列级约束和表记约束

| 约束类型 |                     含义                     |
| :------: | :------------------------------------------: |
| 列级约束 |     只能作用在一个列上，跟在列的定义后面     |
| 表级约束 | 可以作用在多个列上，不与列一起，而是单独定义 |

### NOT NULL 约束

{% alert success no-icon %}

非空约束用于确保当前列的值不为空值，非空约束只能出现在表对象的列上

{% endalert %}

:sparkles: Null 类型特征：
{% alert success no-icon %}

1. 所有的类型的值都可以是 null，包括 int、float 等数据类型
2. 空字符串””不等于 null，0 也不等于 null

{% endalert %}

```sql
#创建 not null 约束：
CREATE TABLE emp(
id INT(10) NOT NULL,
NAME VARCHAR(20) NOT NULL,
sex CHAR NULL
);

#增加 not null 约束：
ALTER TABLE emp
MODIFY sex VARCHAR(30) NOT NULL;

#取消 not null 约束：
ALTER TABLE emp
MODIFY sex VARCHAR(30) NULL;

#取消 not null 约束，增加默认值：
ALTER TABLE emp
MODIFY NAME VARCHAR(15) DEFAULT 'abc' NULL;

```

### UNIQUE 约束

{% alert success no-icon %}

唯一约束，允许出现多个空值（ `NULL` ）

{% endalert %}

同一个表可以有多个唯一约束，多个列组合的约束。在创建唯一约束的时候，如果不给唯一约束名称，就默认和列名相同。

MySQL 会给唯一约束的列上默认创建一个唯一索引

```sql
#创建表时指定唯一键约束
CREATE TABLE USER(
 id INT NOT NULL,
 NAME VARCHAR(25),
 PASSWORD VARCHAR(16),
 #使用表级约束语法
 CONSTRAINT uk_name_pwd UNIQUE(NAME,PASSWORD)
);

#添加唯一约束
#方式一
ALTER TABLE USER
ADD UNIQUE(NAME,PASSWORD);
#方式二
ALTER TABLE USER
ADD CONSTRAINT uk_name_pwd UNIQUE(NAME,PASSWORD);
#方式三
ALTER TABLE USER
MODIFY NAME VARCHAR(20) UNIQUE;

## 删除约束
ALTER TABLE USER
DROP INDEX uk_name_pwd;

```

### PRIMARY KEY 约束

{% alert success no-icon %}

主键约束相当于唯一约束+非空约束的组合，主键约束列不允许重复，也不允许出现空值

{% endalert %}

如果是多列组合的主键约束，那么这些列都不允许为空值，并且组合的值不允许重复。每个表最多只允许一个主键，建立主键约束可以在列级别创建，也可以在表级别上创建。MySQL 的主键名总是 PRIMARY，当创建主键约束时，系统默认会在所在的列和列组合上建立对应的唯一索引。

```sql
## 创建表时，在定义字段时就声明主键约束
CREATE TABLE emp4(
id INT AUTO_INCREMENT PRIMARY KEY,
NAME VARCHAR(20)
);
## 表级模式创建主键约束
CREATE TABLE emp5(
id INT NOT NULL AUTO_INCREMENT,
NAME VARCHAR(20),
pwd VARCHAR(15),
CONSTRAINT emp5_id_pk PRIMARY KEY(id)
);
## 多列组合的主键约束，必须使用表级模式
CREATE TABLE emp6(
id INT NOT NULL,
NAME VARCHAR(20),
pwd VARCHAR(15),
CONSTRAINT emp7_pk PRIMARY KEY(NAME,pwd)
);

## 删除主键约束
ALTER TABLE emp5 DROP PRIMARY KEY;
## 添加主键约束
ALTER TABLE emp5 ADD  PRIMARY KEY(NAME,pwd);
#修改主键约束
ALTER TABLE emp5 MODIFY id INT PRIMARY KEY;

```

### FOREIGN KEY 约束

{% alert success no-icon %}

外键约束是保证一个或两个表之间的参照完整性，外键是构建于一个表的两个字段或是两个表的两个字段之间的参照关系。外键约束只能使用表级模式创建
{% endalert %}

从表的外键值必须在主表中能找到或者为空。当主表的记录被从表参照时，主表的记录将不允许删除，如果要删除数据，需要先删除从表中依赖该记录的数据，然后才可以删除主表的数据。

:notes: 外键约束的参照列，在主表中引用的只能是主键或唯一键约束的列，同一个表可以有多个外键约束

```sql
## 主表
CREATE TABLE classes(
id INT,
NAME VARCHAR(20),
number INT,
PRIMARY KEY(NAME,number)
);

## 从表
CREATE TABLE student(
id INT AUTO_INCREMENT PRIMARY KEY,
classes_name VARCHAR(20),
classes_number INT,
FOREIGN KEY(classes_name,classes_number)
REFERENCES classes(NAME,number)
);

```

```sql
#删除外键约束：
ALTER TABLE emp DROP FOREIGN KEY emp_dept_id_fk;
## 增加外键约束
ALTER TABLE emp ADD [CONSTRAINT emp_dept_id_fk]
FOREIGN KEY(dept_id) REFERENCES dept(dept_id);

```

```sql
ON DELETE CASCADE(级联删除): 当父表中的列被删除时，子表中相对应的列也被删除
ON DELETE SET NULL(级联置空): 当父表中的列被删除时,子表中相应的列置空

```

### CHECK 约束

{% alert success no-icon %}

MySQL 可以使用 check 约束，但 check 约束对数据验证没有任何作用,添加数据时，没有任何错误或警告

{% endalert %}

```sql
CREATE TABLE temp(
id INT AUTO_INCREMENT,
NAME VARCHAR(20),
age INT CHECK(age > 20),
PRIMARY KEY(id)
);

```

### AUTO_INCREMENT

{% alert success no-icon %}

自增长列要求必须设置在一个键上，比如主键或唯一键。自增长列要求数据类型为数值型，并且一个表至多只能有一个自增长列

{% endalert %}

```sql
CREATE TABLE gradeinfo(
	gradeID INT PRIMARY KEY AUTO_INCREMENT,
	gradeName VARCHAR(20)
);
```
