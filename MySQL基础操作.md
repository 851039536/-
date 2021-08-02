# MySQL基础操作

[原文地址](https://www.runoob.com/)

## 创建数据库



```mysql
CREATE DATABASE IF NOT EXISTS RUNOOB DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
```

创建数据库，该命令的作用：

- 1. 如果数据库不存在则创建，存在则不创建。
- 2. 创建RUNOOB数据库，并设定编码集为utf8



## 删除数据库



### drop 命令删除数据库

drop 命令格式：

```
drop database <数据库名>;
```

例如删除名为 RUNOOB 的数据库：

```
mysql> drop database RUNOOB;
```

### 使用 mysqladmin 删除数据库

你也可以使用 mysql **mysqladmin** 命令在终端来执行删除命令。

以下实例删除数据库 RUNOOB(该数据库在前一章节已创建)：



```mysql
[root@host]# mysqladmin -u root -p drop RUNOOB
Enter password:******
```

执行以上删除数据库命令后，会出现一个提示框，来确认是否真的删除数据库：

```mysql
Dropping the database is potentially a very bad thing to do.
Any data stored in the database will be destroyed.

Do you really want to drop the 'RUNOOB' database [y/N] y
Database "RUNOOB" dropped
```



##  数据类型

### 数值类型

MySQL支持所有标准SQL数值数据类型。

这些类型包括严格数值数据类型(INTEGER、SMALLINT、DECIMAL和NUMERIC)，以及近似数值数据类型(FLOAT、REAL和DOUBLE PRECISION)。

关键字INT是INTEGER的同义词，关键字DEC是DECIMAL的同义词。

BIT数据类型保存位字段值，并且支持MyISAM、MEMORY、InnoDB和BDB表。

作为SQL标准的扩展，MySQL也支持整数类型TINYINT、MEDIUMINT和BIGINT。下面的表显示了需要的每个整数类型的存储和范围。

| 类型         | 大小                                     | 范围（有符号）                                               | 范围（无符号）                                               | 用途            |
| :----------- | :--------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :-------------- |
| TINYINT      | 1 字节                                   | (-128，127)                                                  | (0，255)                                                     | 小整数值        |
| SMALLINT     | 2 字节                                   | (-32 768，32 767)                                            | (0，65 535)                                                  | 大整数值        |
| MEDIUMINT    | 3 字节                                   | (-8 388 608，8 388 607)                                      | (0，16 777 215)                                              | 大整数值        |
| INT或INTEGER | 4 字节                                   | (-2 147 483 648，2 147 483 647)                              | (0，4 294 967 295)                                           | 大整数值        |
| BIGINT       | 8 字节                                   | (-9,223,372,036,854,775,808，9 223 372 036 854 775 807)      | (0，18 446 744 073 709 551 615)                              | 极大整数值      |
| FLOAT        | 4 字节                                   | (-3.402 823 466 E+38，-1.175 494 351 E-38)，0，(1.175 494 351 E-38，3.402 823 466 351 E+38) | 0，(1.175 494 351 E-38，3.402 823 466 E+38)                  | 单精度 浮点数值 |
| DOUBLE       | 8 字节                                   | (-1.797 693 134 862 315 7 E+308，-2.225 073 858 507 201 4 E-308)，0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 双精度 浮点数值 |
| DECIMAL      | 对DECIMAL(M,D) ，如果M>D，为M+2否则为D+2 | 依赖于M和D的值                                               | 依赖于M和D的值                                               | 小数值          |

------

### 日期和时间类型

表示时间值的日期和时间类型为DATETIME、DATE、TIMESTAMP、TIME和YEAR。

每个时间类型有一个有效值范围和一个"零"值，当指定不合法的MySQL不能表示的值时使用"零"值。

TIMESTAMP类型有专有的自动更新特性，将在后面描述。

| 类型      | 大小 (字节) | 范围                                                         | 格式                | 用途                     |
| :-------- | :---------- | :----------------------------------------------------------- | :------------------ | :----------------------- |
| DATE      | 3           | 1000-01-01/9999-12-31                                        | YYYY-MM-DD          | 日期值                   |
| TIME      | 3           | '-838:59:59'/'838:59:59'                                     | HH:MM:SS            | 时间值或持续时间         |
| YEAR      | 1           | 1901/2155                                                    | YYYY                | 年份值                   |
| DATETIME  | 8           | 1000-01-01 00:00:00/9999-12-31 23:59:59                      | YYYY-MM-DD HH:MM:SS | 混合日期和时间值         |
| TIMESTAMP | 4           | 1970-01-01 00:00:00/2038结束时间是第 **2147483647** 秒，北京时间 **2038-1-19 11:14:07**，格林尼治时间 2038年1月19日 凌晨 03:14:07 | YYYYMMDD HHMMSS     | 混合日期和时间值，时间戳 |

------

### 字符串类型

字符串类型指CHAR、VARCHAR、BINARY、VARBINARY、BLOB、TEXT、ENUM和SET。该节描述了这些类型如何工作以及如何在查询中使用这些类型。

| 类型       | 大小                | 用途                            |
| :--------- | :------------------ | :------------------------------ |
| CHAR       | 0-255字节           | 定长字符串                      |
| VARCHAR    | 0-65535 字节        | 变长字符串                      |
| TINYBLOB   | 0-255字节           | 不超过 255 个字符的二进制字符串 |
| TINYTEXT   | 0-255字节           | 短文本字符串                    |
| BLOB       | 0-65 535字节        | 二进制形式的长文本数据          |
| TEXT       | 0-65 535字节        | 长文本数据                      |
| MEDIUMBLOB | 0-16 777 215字节    | 二进制形式的中等长度文本数据    |
| MEDIUMTEXT | 0-16 777 215字节    | 中等长度文本数据                |
| LONGBLOB   | 0-4 294 967 295字节 | 二进制形式的极大文本数据        |
| LONGTEXT   | 0-4 294 967 295字节 | 极大文本数据                    |

CHAR 和 VARCHAR 类型类似，但它们保存和检索的方式不同。它们的最大长度和是否尾部空格被保留等方面也不同。在存储或检索过程中不进行大小写转换。

BINARY 和 VARBINARY 类似于 CHAR 和 VARCHAR，不同的是它们包含二进制字符串而不要非二进制字符串。也就是说，它们包含字节字符串而不是字符字符串。这说明它们没有字符集，并且排序和比较基于列值字节的数值值。

BLOB 是一个二进制大对象，可以容纳可变数量的数据。有 4 种 BLOB 类型：TINYBLOB、BLOB、MEDIUMBLOB 和 LONGBLOB。它们区别在于可容纳存储范围不同。

有 4 种 TEXT 类型：TINYTEXT、TEXT、MEDIUMTEXT 和 LONGTEXT。对应的这 4 种 BLOB 类型，可存储的最大长度不同，可根据实际情况选择。



## 创建数据表



创建MySQL数据表需要以下信息：

- 表名
- 表字段名
- 定义每个表字段

### 语法

以下为创建MySQL数据表的SQL通用语法：

```mysql
CREATE TABLE table_name (column_name column_type);
```



以下例子中我们将在 RUNOOB 数据库中创建数据表runoob_tbl：

```mysql
CREATE TABLE IF NOT EXISTS `runoob_tbl`(
   `runoob_id` INT UNSIGNED AUTO_INCREMENT,
   `runoob_title` VARCHAR(100) NOT NULL,
   `runoob_author` VARCHAR(40) NOT NULL,
   `submission_date` DATE,
   PRIMARY KEY ( `runoob_id` )
)ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

实例解析：

- 如果你不想字段为 **NULL** 可以设置字段的属性为 **NOT NULL**， 在操作数据库时如果输入该字段的数据为**NULL** ，就会报错。

- AUTO_INCREMENT定义列为自增的属性，一般用于主键，数值会自动加1。

- PRIMARY KEY关键字用于定义列为主键。 您可以使用多列来定义主键，列间以逗号分隔。

- ENGINE 设置存储引擎，CHARSET 设置编码。

  

------

### 通过命令提示符创建表

通过 mysql> 命令窗口可以很简单的创建MySQL数据表。你可以使用 SQL 语句 **CREATE TABLE** 来创建数据表。

### 实例

以下为创建数据表 runoob_tbl 实例:

```mysql
root@host# mysql -u root -p
Enter password:*******
mysql> use RUNOOB;
Database changed
mysql> CREATE TABLE runoob_tbl(
   -> runoob_id INT NOT NULL AUTO_INCREMENT,
   -> runoob_title VARCHAR(100) NOT NULL,
   -> runoob_author VARCHAR(40) NOT NULL,
   -> submission_date DATE,
   -> PRIMARY KEY ( runoob_id )
   -> )ENGINE=InnoDB DEFAULT CHARSET=utf8;
Query OK, 0 rows affected (0.16 sec)
mysql>
```

**注意：**MySQL命令终止符为分号 **;** 。

**注意：** **->** 是换行符标识，不要复制。



## 删除数据表



以下为删除MySQL数据表的通用语法：

```mysql
DROP TABLE table_name ;
```



### 在命令提示窗口中删除数据表

在mysql>命令提示窗口中删除数据表SQL语句为 **DROP TABLE** ：

### 实例

以下实例删除了数据表runoob_tbl:

```mysql
root@host# mysql -u root -p
Enter password:*******
mysql> use RUNOOB;
Database changed
mysql> DROP TABLE runoob_tbl
Query OK, 0 rows affected (0.8 sec)
mysql>
```



## 插入数据

MySQL 表中使用 **INSERT INTO** SQL语句来插入数据。

```mysql
INSERT INTO table_name ( field1, field2,...fieldN )
                       VALUES
                       ( value1, value2,...valueN );
```

如果数据是字符型，必须使用单引号或者双引号，如："value"。



### 通过命令提示窗口插入数据

以下我们将使用 SQL **INSERT INTO** 语句向 MySQL 数据表 runoob_tbl 插入数据

以下实例中我们将向 runoob_tbl 表插入三条数据:

```mysql
root@host# mysql -u root -p password;
Enter password:*******
mysql> use RUNOOB;
Database changed
mysql> INSERT INTO runoob_tbl 
    -> (runoob_title, runoob_author, submission_date)
    -> VALUES
    -> ("学习 PHP", "菜鸟教程", NOW());
Query OK, 1 rows affected, 1 warnings (0.01 sec)
mysql> INSERT INTO runoob_tbl
    -> (runoob_title, runoob_author, submission_date)
    -> VALUES
    -> ("学习 MySQL", "菜鸟教程", NOW());
Query OK, 1 rows affected, 1 warnings (0.01 sec)
mysql> INSERT INTO runoob_tbl
    -> (runoob_title, runoob_author, submission_date)
    -> VALUES
    -> ("JAVA 教程", "RUNOOB.COM", '2016-05-06');
Query OK, 1 rows affected (0.00 sec)
mysql>
```



## 查询数据

### 语法

以下为在MySQL数据库中查询数据通用的 SELECT 语法：

```mysql
SELECT column_name,column_name
FROM table_name
[WHERE Clause]
[LIMIT N][ OFFSET M]
```

- 查询语句中你可以使用一个或者多个表，表之间使用逗号(,)分割，并使用WHERE语句来设定查询条件。
- SELECT 命令可以读取一条或者多条记录。
- 你可以使用星号（*）来代替其他字段，SELECT语句会返回表的所有字段数据
- 你可以使用 WHERE 语句来包含任何条件。
- 你可以使用 LIMIT 属性来设定返回的记录数。
- 你可以通过OFFSET指定SELECT语句开始查询的数据偏移量。默认情况下偏移量为0。

## WHERE 子句

### 语法

以下是 SQL SELECT 语句使用 WHERE 子句从数据表中读取数据的通用语法：

```
SELECT field1, field2,...fieldN FROM table_name1, table_name2...
[WHERE condition1 [AND [OR]] condition2.....
```

- 查询语句中你可以使用一个或者多个表，表之间使用逗号**,** 分割，并使用WHERE语句来设定查询条件。
- 你可以在 WHERE 子句中指定任何条件。
- 你可以使用 AND 或者 OR 指定一个或多个条件。
- WHERE 子句也可以运用于 SQL 的 DELETE 或者 UPDATE 命令。
- WHERE 子句类似于程序语言中的 if 条件，根据 MySQL 表中的字段值来读取指定的数据。

以下为操作符列表，可用于 WHERE 子句中。

下表中实例假定 A 为 10, B 为 20

| 操作符 | 描述                                                         | 实例                 |
| :----- | :----------------------------------------------------------- | :------------------- |
| =      | 等号，检测两个值是否相等，如果相等返回true                   | (A = B) 返回false。  |
| <>, != | 不等于，检测两个值是否相等，如果不相等返回true               | (A != B) 返回 true。 |
| >      | 大于号，检测左边的值是否大于右边的值, 如果左边的值大于右边的值返回true | (A > B) 返回false。  |
| <      | 小于号，检测左边的值是否小于右边的值, 如果左边的值小于右边的值返回true | (A < B) 返回 true。  |
| >=     | 大于等于号，检测左边的值是否大于或等于右边的值, 如果左边的值大于或等于右边的值返回true | (A >= B) 返回false。 |
| <=     | 小于等于号，检测左边的值是否小于或等于右边的值, 如果左边的值小于或等于右边的值返回true | (A <= B) 返回 true。 |

如果我们想在 MySQL 数据表中读取指定的数据，WHERE 子句是非常有用的。

使用主键来作为 WHERE 子句的条件查询是非常快速的。

如果给定的条件在表中没有任何匹配的记录，那么查询不会返回任何数据。

------

### 从命令提示符中读取数据

我们将在SQL SELECT语句使用WHERE子句来读取MySQL数据表 runoob_tbl 中的数据：

实例

以下实例将读取 runoob_tbl 表中 runoob_author 字段值为 Sanjay 的所有记录：

### SQL SELECT WHERE 子句

`SELECT * from runoob_tbl WHERE runoob_author='菜鸟教程';`





## UPDATE 更新

### 语法

以下是 UPDATE 命令修改 MySQL 数据表数据的通用 SQL 语法：

```mysql
UPDATE table_name SET field1=new-value1, field2=new-value2
[WHERE Clause]
```

- 你可以同时更新一个或多个字段。
- 你可以在 WHERE 子句中指定任何条件。
- 你可以在一个单独表中同时更新数据。



## DELETE 语句

### 语法

以下是 SQL DELETE 语句从 MySQL 数据表中删除数据的通用语法：

```
DELETE FROM table_name [WHERE Clause]
```

- 如果没有指定 WHERE 子句，MySQL 表中的所有记录将被删除。
- 你可以在 WHERE 子句中指定任何条件
- 您可以在单个表中一次性删除记录。

当你想删除数据表中指定的记录时 WHERE 子句是非常有用的。

------

### 从命令行中删除数据

这里我们将在 SQL DELETE 命令中使用 WHERE 子句来删除 MySQL 数据表 runoob_tbl 所选的数据。

### 实例

以下实例将删除 runoob_tbl 表中 runoob_id 为3 的记录：

### DELETE 语句：

`mysql> use RUNOOB; Database changed mysql> DELETE FROM runoob_tbl WHERE runoob_id=3; Query OK, 1 row affected (0.23 sec)`



## LIKE 子句

### 在命令提示符中使用 LIKE 子句

以下我们将在 SQL SELECT 命令中使用 WHERE...LIKE 子句来从MySQL数据表 runoob_tbl 中读取数据。

### 实例

以下是我们将 runoob_tbl 表中获取 runoob_author 字段中以 **COM** 为结尾的的所有记录：

###  SQL LIKE 语句：

```mysql
mysql> use RUNOOB; 
Database changed 
mysql> SELECT * from runoob_tbl  WHERE runoob_author LIKE '%COM';

```

like 匹配/模糊匹配，会与 **%** 和 **_** 结合使用。

```
'%a'     //以a结尾的数据
'a%'     //以a开头的数据
'%a%'    //含有a的数据
'_a_'    //三位且中间字母是a的
'_a'     //两位且结尾字母是a的
'a_'     //两位且开头字母是a的
```

查询以 java 字段开头的信息。

```
SELECT * FROM position WHERE name LIKE 'java%';
```

查询包含 java 字段的信息。

```
SELECT * FROM position WHERE name LIKE '%java%';
```

查询以 java 字段结尾的信息。

```
SELECT * FROM position WHERE name LIKE '%java';
```



## UNION 操作符

### 描述

MySQL UNION 操作符用于连接两个以上的 SELECT 语句的结果组合到一个结果集合中。多个 SELECT 语句会删除重复的数据。



### 语法

MySQL UNION 操作符语法格式：

```
SELECT expression1, expression2, ... expression_n
FROM tables
[WHERE conditions]
UNION [ALL | DISTINCT]
SELECT expression1, expression2, ... expression_n
FROM tables
[WHERE conditions];
```

### 参数

- **expression1, expression2, ... expression_n**: 要检索的列。
- **tables:** 要检索的数据表。
- **WHERE conditions:** 可选， 检索条件。
- **DISTINCT:** 可选，删除结果集中重复的数据。默认情况下 UNION 操作符已经删除了重复数据，所以 DISTINCT 修饰符对结果没啥影响。
- **ALL:** 可选，返回所有结果集，包含重复数据。

### UNION 实例

下面的 SQL 语句从 "Websites" 和 "apps" 表中选取所有**不同的**country（只有不同的值）：

### 实例

```mysql
SELECT country FROM Websites
UNION
SELECT country FROM apps
ORDER BY country;
```

### SQL UNION ALL 实例

下面的 SQL 语句使用 UNION ALL 从 "Websites" 和 "apps" 表中选取**所有的**country（也有重复的值）：

### 实例

```mysql
SELECT country FROM Websites
UNION ALL
SELECT country FROM apps
ORDER BY country;
```

### 带有 WHERE 的 SQL UNION ALL

下面的 SQL 语句使用 UNION ALL 从 "Websites" 和 "apps" 表中选取**所有的**中国(CN)的数据（也有重复的值）：

### 实例

```mysql
SELECT country, name FROM Websites
WHERE country='CN'
UNION ALL
SELECT country, app_name FROM apps
WHERE country='CN'
ORDER BY country;
```



**UNION 语句**：用于将不同表中相同列中查询的数据展示出来；（不包括重复数据）

**UNION ALL 语句**：用于将不同表中相同列中查询的数据展示出来；（包括重复数据）

使用形式如下：

```mysql
SELECT 列名称 FROM 表名称 UNION SELECT 列名称 FROM 表名称 ORDER BY 列名称；
SELECT 列名称 FROM 表名称 UNION ALL SELECT 列名称 FROM 表名称 ORDER BY 列名称；
```



##  排序



### 语法

以下是 SQL SELECT 语句使用 ORDER BY 子句将查询数据排序后再返回数据：

```mysql
SELECT field1, field2,...fieldN FROM table_name1, table_name2...
ORDER BY field1 [ASC [DESC][默认 ASC]], [field2...] [ASC [DESC][默认 ASC]]
```

- 你可以使用任何字段来作为排序的条件，从而返回排序后的查询结果。
- 你可以设定多个字段来排序。
- 你可以使用 ASC 或 DESC 关键字来设置查询结果是按升序或降序排列。 默认情况下，它是按升序排列。
- 你可以添加 WHERE...LIKE 子句来设置条件。



###  ORDER BY 子句

以下将在 SQL SELECT 语句中使用 ORDER BY 子句来读取MySQL 数据表 runoob_tbl 中的数据：

### 实例

尝试以下实例，结果将按升序及降序排列。

### SQL 排序

```mysql
mysql> use RUNOOB; Database changed mysql> SELECT * from runoob_tbl ORDER BY submission_date ASC;
```





MySQL 排序我们知道从 MySQL 表中使用 SQL SELECT 语句来读取：

**MySQL 拼音排序**

如果字符集采用的是 gbk(汉字编码字符集)，直接在查询语句后边添加 ORDER BY：

```mysql
SELECT * 
FROM runoob_tbl
ORDER BY runoob_title;
```

如果字符集采用的是 utf8(万国码)，需要先对字段进行转码然后排序：

```mysql
SELECT * 
FROM runoob_tbl
ORDER BY CONVERT(runoob_title using gbk);
```



## GROUP BY 语句

GROUP BY 语句根据一个或多个列对结果集进行分组。

在分组的列上我们可以使用 COUNT, SUM, AVG,等函数。

### GROUP BY 语法

```mysql
SELECT column_name, function(column_name)
FROM table_name
WHERE column_name operator value
GROUP BY column_name;
```

 GROUP BY 语句 将数据表按名字进行分组，并统计每个人有多少条记录：

```mysql
mysql> SELECT name, COUNT(*) FROM   employee_tbl GROUP BY name;
```

### 使用 WITH ROLLUP

WITH ROLLUP 可以实现在分组统计数据基础上再进行相同的统计（SUM,AVG,COUNT…）。

例如我们将以上的数据表按名字进行分组，再统计每个人登录的次数：

```MYSQL
mysql> SELECT name, SUM(singin) as singin_count FROM  employee_tbl GROUP BY name WITH ROLLUP;
```



## JOIN 连接的使用

SELECT, UPDATE 和 DELETE 语句中使用 Mysql 的 JOIN 来联合多表查询。

JOIN 按照功能大致分为如下三类：

- **INNER JOIN（内连接,或等值连接）**：获取两个表中字段匹配关系的记录。
- **LEFT JOIN（左连接）：**获取左表所有记录，即使右表没有对应匹配的记录。
- **RIGHT JOIN（右连接）：** 与 LEFT JOIN 相反，用于获取右表所有记录，即使左表没有对应匹配的记录。



### 使用 INNER JOIN

```MYSQL
mysql> SELECT a.runoob_id, a.runoob_author, b.runoob_count FROM runoob_tbl a INNER JOIN tcount_tbl b ON a.runoob_author = b.runoob_author;
```

### WHERE 子句

```MYSQL
mysql> SELECT a.runoob_id, a.runoob_author, b.runoob_count FROM runoob_tbl a, tcount_tbl b WHERE a.runoob_author = b.runoob_author;


```

### LEFT JOIN

MySQL left join 与 join 有所不同。 MySQL LEFT JOIN 会读取左边数据表的全部数据，即便右边表无对应数据。

```mysql
mysql> SELECT a.runoob_id, a.runoob_author, b.runoob_count FROM runoob_tbl a LEFT JOIN tcount_tbl b ON a.runoob_author = b.runoob_author;
```

### RIGHT JOIN

MySQL RIGHT JOIN 会读取右边数据表的全部数据，即便左边边表无对应数据。

```mysql
mysql> SELECT a.runoob_id, a.runoob_author, b.runoob_count FROM runoob_tbl a RIGHT JOIN tcount_tbl b ON a.runoob_author = b.runoob_author;
```



## NULL 值处理

MySQL提供了三大运算符:

- **IS NULL:** 当列的值是 NULL,此运算符返回 true。
- **IS NOT NULL:** 当列的值不为 NULL, 运算符返回 true。
- **<=>:** 比较操作符（不同于 = 运算符），当比较的的两个值相等或者都为 NULL 时返回 true。

### 使用 NULL 值

查找数据表中 runoob_test_tbl 列是否为 NULL，必须使用 **IS NULL** 和 **IS NOT NULL**，如下实例：

```mysql
mysql> SELECT * FROM runoob_test_tbl WHERE runoob_count IS NULL;


```

## 正则表达式



| 模式       | 描述                                                         |
| :--------- | :----------------------------------------------------------- |
| ^          | 匹配输入字符串的开始位置。如果设置了 RegExp 对象的 Multiline 属性，^ 也匹配 '\n' 或 '\r' 之后的位置。 |
| $          | 匹配输入字符串的结束位置。如果设置了RegExp 对象的 Multiline 属性，$ 也匹配 '\n' 或 '\r' 之前的位置。 |
| .          | 匹配除 "\n" 之外的任何单个字符。要匹配包括 '\n' 在内的任何字符，请使用象 '[.\n]' 的模式。 |
| [...]      | 字符集合。匹配所包含的任意一个字符。例如， '[abc]' 可以匹配 "plain" 中的 'a'。 |
| [^...]     | 负值字符集合。匹配未包含的任意字符。例如， '[^abc]' 可以匹配 "plain" 中的'p'。 |
| p1\|p2\|p3 | 匹配 p1 或 p2 或 p3。例如，'z\|food' 能匹配 "z" 或 "food"。'(z\|f)ood' 则匹配 "zood" 或 "food"。 |
| *          | 匹配前面的子表达式零次或多次。例如，zo* 能匹配 "z" 以及 "zoo"。* 等价于{0,}。 |
| +          | 匹配前面的子表达式一次或多次。例如，'zo+' 能匹配 "zo" 以及 "zoo"，但不能匹配 "z"。+ 等价于 {1,}。 |
| {n}        | n 是一个非负整数。匹配确定的 n 次。例如，'o{2}' 不能匹配 "Bob" 中的 'o'，但是能匹配 "food" 中的两个 o。 |
| {n,m}      | m 和 n 均为非负整数，其中n <= m。最少匹配 n 次且最多匹配 m 次。 |

### 实例

了解以上的正则需求后，我们就可以根据自己的需求来编写带有正则表达式的SQL语句。以下我们将列出几个小实例(表名：person_tbl )来加深我们的理解：

查找name字段中以'st'为开头的所有数据：

```mysql
mysql> SELECT name FROM person_tbl WHERE name REGEXP '^st';
```

查找name字段中以'ok'为结尾的所有数据：

```mysql
mysql> SELECT name FROM person_tbl WHERE name REGEXP 'ok$';
```

查找name字段中包含'mar'字符串的所有数据：

```mysql
mysql> SELECT name FROM person_tbl WHERE name REGEXP 'mar';
```

查找name字段中以元音字符开头或以'ok'字符串结尾的所有数据：

```mysql
mysql> SELECT name FROM person_tbl WHERE name REGEXP '^[aeiou]|ok$';
```



## 事务

MySQL 事务主要用于处理操作量大，复杂度高的数据。比如说，在人员管理系统中，你删除一个人员，你既需要删除人员的基本资料，也要删除和该人员相关的信息，如信箱，文章等等，这样，这些数据库操作语句就构成一个事务！

- 在 MySQL 中只有使用了 Innodb 数据库引擎的数据库或表才支持事务。
- 事务处理可以用来维护数据库的完整性，保证成批的 SQL 语句要么全部执行，要么全部不执行。
- 事务用来管理 insert,update,delete 语句

一般来说，事务是必须满足4个条件（ACID）：：原子性（**A**tomicity，或称不可分割性）、一致性（**C**onsistency）、隔离性（**I**solation，又称独立性）、持久性（**D**urability）。

- **原子性：**一个事务（transaction）中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。
- **一致性：**在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的预设规则，这包含资料的精确度、串联性以及后续数据库可以自发性地完成预定的工作。
- **隔离性：**数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括读未提交（Read uncommitted）、读提交（read committed）、可重复读（repeatable read）和串行化（Serializable）。
- **持久性：**事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。

### 事务控制语句：

- BEGIN 或 START TRANSACTION 显式地开启一个事务；
- COMMIT 也可以使用 COMMIT WORK，不过二者是等价的。COMMIT 会提交事务，并使已对数据库进行的所有修改成为永久性的；
- ROLLBACK 也可以使用 ROLLBACK WORK，不过二者是等价的。回滚会结束用户的事务，并撤销正在进行的所有未提交的修改；
- SAVEPOINT identifier，SAVEPOINT 允许在事务中创建一个保存点，一个事务中可以有多个 SAVEPOINT；
- RELEASE SAVEPOINT identifier 删除一个事务的保存点，当没有指定的保存点时，执行该语句会抛出一个异常；
- ROLLBACK TO identifier 把事务回滚到标记点；
- SET TRANSACTION 用来设置事务的隔离级别。InnoDB 存储引擎提供事务的隔离级别有READ UNCOMMITTED、READ COMMITTED、REPEATABLE READ 和 SERIALIZABLE。

### MYSQL 事务处理主要有两种方法：

1、用 BEGIN, ROLLBACK, COMMIT来实现

- **BEGIN** 开始一个事务
- **ROLLBACK** 事务回滚
- **COMMIT** 事务确认

2、直接用 SET 来改变 MySQL 的自动提交模式:

- **SET AUTOCOMMIT=0** 禁止自动提交
- **SET AUTOCOMMIT=1** 开启自动提交



```mysql
mysql> use RUNOOB;
Database changed
mysql> CREATE TABLE runoob_transaction_test( id int(5)) engine=innodb;  # 创建数据表
Query OK, 0 rows affected (0.04 sec)
 
mysql> select * from runoob_transaction_test;
Empty set (0.01 sec)
 
mysql> begin;  # 开始事务
Query OK, 0 rows affected (0.00 sec)
 
mysql> insert into runoob_transaction_test value(5);
Query OK, 1 rows affected (0.01 sec)
 
mysql> insert into runoob_transaction_test value(6);
Query OK, 1 rows affected (0.00 sec)
 
mysql> commit; # 提交事务
Query OK, 0 rows affected (0.01 sec)
 
mysql>  select * from runoob_transaction_test;
+------+
| id   |
+------+
| 5    |
| 6    |
+------+
2 rows in set (0.01 sec)
 
mysql> begin;    # 开始事务
Query OK, 0 rows affected (0.00 sec)
 
mysql>  insert into runoob_transaction_test values(7);
Query OK, 1 rows affected (0.00 sec)
 
mysql> rollback;   # 回滚
Query OK, 0 rows affected (0.00 sec)
 
mysql>   select * from runoob_transaction_test;   # 因为回滚所以数据没有插入
+------+
| id   |
+------+
| 5    |
| 6    |
+------+
2 rows in set (0.01 sec)
 
mysql>
```



## ALTER命令

当我们需要修改数据表名或者修改数据表字段时，就需要使用到MySQL ALTER命令。

### 删除，添加或修改表字段

如下命令使用了 ALTER 命令及 DROP 子句来删除以上创建表的 i 字段：

```mysql
mysql> ALTER TABLE testalter_tbl  DROP i;
```

如果数据表中只剩余一个字段则无法使用DROP来删除字段。

MySQL 中使用 ADD 子句来向数据表中添加列，如下实例在表 testalter_tbl 中添加 i 字段，并定义数据类型:

```mysql
mysql> ALTER TABLE testalter_tbl ADD i INT;
```

## 修改字段类型及名称

如果需要修改字段类型及名称, 你可以在ALTER命令中使用 MODIFY 或 CHANGE 子句 。

例如，把字段 c 的类型从 CHAR(1) 改为 CHAR(10)，可以执行以下命令:

```mysql
mysql> ALTER TABLE testalter_tbl MODIFY c CHAR(10);
```

使用 CHANGE 子句, 语法有很大的不同。 在 CHANGE 关键字之后，紧跟着的是你要修改的字段名，然后指定新字段名及类型。尝试如下实例：

```mysql
mysql> ALTER TABLE testalter_tbl CHANGE i j BIGINT;
mysql> ALTER TABLE testalter_tbl CHANGE j j INT;
```

------

### ALTER TABLE 对 Null 值和默认值的影响

当你修改字段时，你可以指定是否包含值或者是否设置默认值。

以下实例，指定字段 j 为 NOT NULL 且默认值为100 。

```mysql
mysql> ALTER TABLE testalter_tbl 
    -> MODIFY j BIGINT NOT NULL DEFAULT 100;
```

如果你不设置默认值，MySQL会自动设置该字段默认为 NULL。

------

### 修改字段默认值

你可以使用 ALTER 来修改字段的默认值，尝试以下实例：

```mysql
mysql> ALTER TABLE testalter_tbl ALTER i SET DEFAULT 1000;
mysql> SHOW COLUMNS FROM testalter_tbl;
```

### 修改表名

如果需要修改数据表的名称，可以在 ALTER TABLE 语句中使用 RENAME 子句来实现。

尝试以下实例将数据表 testalter_tbl 重命名为 alter_tbl：

```mysql
mysql> ALTER TABLE testalter_tbl RENAME TO alter_tbl;
```



### alter其他用途：

修改存储引擎：修改为myisam

```mysql
alter table tableName engine=myisam;
```

删除外键约束：keyName是外键别名

```mysql
alter table tableName drop foreign key keyName;
```

修改字段的相对位置：这里name1为想要修改的字段，type1为该字段原来类型，first和after二选一，这应该显而易见，first放在第一位，after放在name2字段后面

```mysql
alter table tableName modify name1 type1 first|after name2;
```



## 索引

MySQL索引的建立对于MySQL的高效运行是很重要的，索引可以大大提高MySQL的检索速度。

打个比方，如果合理的设计且使用索引的MySQL是一辆兰博基尼的话，那么没有设计和使用索引的MySQL就是一个人力三轮车。

拿汉语字典的目录页（索引）打比方，我们可以按拼音、笔画、偏旁部首等排序的目录（索引）快速查找到需要的字。

索引分单列索引和组合索引。单列索引，即一个索引只包含单个列，一个表可以有多个单列索引，但这不是组合索引。组合索引，即一个索引包含多个列。

创建索引时，你需要确保该索引是应用在 SQL 查询语句的条件(一般作为 WHERE 子句的条件)。

实际上，索引也是一张表，该表保存了主键与索引字段，并指向实体表的记录。

上面都在说使用索引的好处，但过多的使用索引将会造成滥用。因此索引也会有它的缺点：虽然索引大大提高了查询速度，同时却会降低更新表的速度，如对表进行INSERT、UPDATE和DELETE。因为更新表时，MySQL不仅要保存数据，还要保存一下索引文件。

建立索引会占用磁盘空间的索引文件。



### 普通索引

### 创建索引

这是最基本的索引，它没有任何限制。它有以下几种创建方式：

```mysql
CREATE INDEX indexName ON mytable(username(length)); 
```

如果是CHAR，VARCHAR类型，length可以小于字段实际长度；如果是BLOB和TEXT类型，必须指定 length。

### 修改表结构(添加索引)

```mysql
ALTER table tableName ADD INDEX indexName(columnName)
```

### 创建表的时候直接指定

```mysql
CREATE TABLE mytable(  
 
ID INT NOT NULL,   
 
username VARCHAR(16) NOT NULL,  
 
INDEX [indexName] (username(length))  
 
);  
```

### 删除索引的语法

```mysql
DROP INDEX [indexName] ON mytable; 
```

------

### 唯一索引

它与前面的普通索引类似，不同的就是：索引列的值必须唯一，但允许有空值。如果是组合索引，则列值的组合必须唯一。它有以下几种创建方式：

### 创建索引

```mysql
CREATE UNIQUE INDEX indexName ON mytable(username(length)) 
```

### 修改表结构

```mysql
mysqlALTER table mytable ADD UNIQUE [indexName] (username(length))
```

### 创建表的时候直接指定

```mysql
CREATE TABLE mytable(  
 
ID INT NOT NULL,   
 
username VARCHAR(16) NOT NULL,  
 
UNIQUE [indexName] (username(length))  
 
);  
```

------

### 使用ALTER 命令添加和删除索引

有四种方式来添加数据表的索引：

- ALTER TABLE tbl_name ADD PRIMARY KEY (column_list):

   

  该语句添加一个主键，这意味着索引值必须是唯一的，且不能为NULL。

  

- **ALTER TABLE tbl_name ADD UNIQUE index_name (column_list):** 这条语句创建索引的值必须是唯一的（除了NULL外，NULL可能会出现多次）。

- **ALTER TABLE tbl_name ADD INDEX index_name (column_list):** 添加普通索引，索引值可出现多次。

- **ALTER TABLE tbl_name ADD FULLTEXT index_name (column_list):**该语句指定了索引为 FULLTEXT ，用于全文索引。

以下实例为在表中添加索引。

```mysql
mysql> ALTER TABLE testalter_tbl ADD INDEX (c);
```

你还可以在 ALTER 命令中使用 DROP 子句来删除索引。尝试以下实例删除索引:

```mysql
mysql> ALTER TABLE testalter_tbl DROP INDEX c;
```

------

### 使用 ALTER 命令添加和删除主键

主键只能作用于一个列上，添加主键索引时，你需要确保该主键默认不为空（NOT NULL）。实例如下：

```mysql
mysql> ALTER TABLE testalter_tbl MODIFY i INT NOT NULL;
mysql> ALTER TABLE testalter_tbl ADD PRIMARY KEY (i);
```

你也可以使用 ALTER 命令删除主键：

```mysql
mysql> ALTER TABLE testalter_tbl DROP PRIMARY KEY;
```

删除主键时只需指定PRIMARY KEY，但在删除索引时，你必须知道索引名。

------

### 显示索引信息

你可以使用 SHOW INDEX 命令来列出表中的相关的索引信息。可以通过添加 \G 来格式化输出信息。

尝试以下实例:

```mysql
mysql> SHOW INDEX FROM table_name; \G
........
```

## 临时表

MySQL 临时表在我们需要保存一些临时数据时是非常有用的。临时表只在当前连接可见，当关闭连接时，Mysql会自动删除表并释放所有空间。



## 复制表

如果我们需要完全的复制MySQL的数据表，包括表的结构，索引，默认值等。 如果仅仅使用**CREATE TABLE ... SELECT** 命令，是无法实现的。

本章节将为大家介绍如何完整的复制MySQL数据表，步骤如下：

- 使用 **SHOW CREATE TABLE** 命令获取创建数据表(**CREATE TABLE**) 语句，该语句包含了原数据表的结构，索引等。
- 
- 复制以下命令显示的SQL语句，修改数据表名，并执行SQL语句，通过以上命令 将完全的复制数据表结构。
- 如果你想复制表的内容，你就可以使用 **INSERT INTO ... SELECT** 语句来实现。

**mysql复制表的两种方式。**

**第一、只复制表结构到新表**

create table 新表 select * from 旧表 where 1=2

或者

create table 新表 like 旧表 

**第二、复制表结构及数据到新表**

create table新表 select * from 旧表 



### 实例

尝试以下实例来复制表 runoob_tbl 。

**步骤一：**

获取数据表的完整结构。

```mysql
mysql> SHOW CREATE TABLE runoob_tbl \G;
*************************** 1. row ***************************
       Table: runoob_tbl
Create Table: CREATE TABLE `runoob_tbl` (
  `runoob_id` int(11) NOT NULL auto_increment,
  `runoob_title` varchar(100) NOT NULL default '',
  `runoob_author` varchar(40) NOT NULL default '',
  `submission_date` date default NULL,
  PRIMARY KEY  (`runoob_id`),
  UNIQUE KEY `AUTHOR_INDEX` (`runoob_author`)
) ENGINE=InnoDB 
1 row in set (0.00 sec)

ERROR:
No query specified
```

**步骤二：**

修改SQL语句的数据表名，并执行SQL语句。

```mysql
mysql> CREATE TABLE `clone_tbl` (
  -> `runoob_id` int(11) NOT NULL auto_increment,
  -> `runoob_title` varchar(100) NOT NULL default '',
  -> `runoob_author` varchar(40) NOT NULL default '',
  -> `submission_date` date default NULL,
  -> PRIMARY KEY  (`runoob_id`),
  -> UNIQUE KEY `AUTHOR_INDEX` (`runoob_author`)
-> ) ENGINE=InnoDB;
Query OK, 0 rows affected (1.80 sec)
```

**步骤三：**

执行完第二步骤后，你将在数据库中创建新的克隆表 clone_tbl。 如果你想拷贝数据表的数据你可以使用 **INSERT INTO... SELECT** 语句来实现。

```mysql
mysql> INSERT INTO clone_tbl (runoob_id,
    ->                        runoob_title,
    ->                        runoob_author,
    ->                        submission_date)
    -> SELECT runoob_id,runoob_title,
    ->        runoob_author,submission_date
    -> FROM runoob_tbl;
Query OK, 3 rows affected (0.07 sec)
Records: 3  Duplicates: 0  Warnings: 0
```

执行以上步骤后，你将完整的复制表，包括表结构及表数据。



### 完整复制表的方法

```mysql
CREATE TABLE targetTable LIKE sourceTable;
INSERT INTO targetTable SELECT * FROM sourceTable;
```

其他:

可以拷贝一个表中其中的一些字段:

```mysql
CREATE TABLE newadmin AS
(
    SELECT username, password FROM admin
)
```

可以将新建的表的字段改名:

```mysql
CREATE TABLE newadmin AS
(  
    SELECT id, username AS uname, password AS pass FROM admin
)
```

可以拷贝一部分数据:

```mysql
CREATE TABLE newadmin AS
(
    SELECT * FROM admin WHERE LEFT(username,1) = 's'
)
```

可以在创建表的同时定义表中的字段信息:

```mysql
CREATE TABLE newadmin
(
    id INTEGER NOT NULL AUTO_INCREMENT PRIMARY KEY
)
AS
(
    SELECT * FROM admin
)  
```



## 元数据

你可能想知道MySQL以下三种信息：

- **查询结果信息：** SELECT, UPDATE 或 DELETE语句影响的记录数。
- **数据库和数据表的信息：** 包含了数据库及数据表的结构信息。
- **MySQL服务器信息：** 包含了数据库服务器的当前状态，版本号等。

### 获取服务器元数据

以下命令语句可以在 MySQL 的命令提示符使用，也可以在脚本中 使用，如PHP脚本。

| 命令               | 描述                      |
| :----------------- | :------------------------ |
| SELECT VERSION( )  | 服务器版本信息            |
| SELECT DATABASE( ) | 当前数据库名 (或者返回空) |
| SELECT USER( )     | 当前用户名                |
| SHOW STATUS        | 服务器状态                |
| SHOW VARIABLES     | 服务器配置变量            |



## 序列使用

MySQL 序列是一组整数：1, 2, 3, ...，由于一张数据表只能有一个字段自增主键， 如果你想实现其他字段也实现自动增加，就可以使用MySQL序列来实现。



### 使用 AUTO_INCREMENT

MySQL 中最简单使用序列的方法就是使用 MySQL AUTO_INCREMENT 来定义列。

```mysql
mysql> CREATE TABLE insect
    -> (
    -> id INT UNSIGNED NOT NULL AUTO_INCREMENT,
    -> PRIMARY KEY (id),
    -> name VARCHAR(30) NOT NULL, # type of insect
    -> date DATE NOT NULL, # date collected
    -> origin VARCHAR(30) NOT NULL # where collected
);
Query OK, 0 rows affected (0.02 sec)
mysql> INSERT INTO insect (id,name,date,origin) VALUES
    -> (NULL,'housefly','2001-09-10','kitchen'),
    -> (NULL,'millipede','2001-09-10','driveway'),
    -> (NULL,'grasshopper','2001-09-10','front yard');
Query OK, 3 rows affected (0.02 sec)
Records: 3  Duplicates: 0  Warnings: 0
mysql> SELECT * FROM insect ORDER BY id;
```



### 获取AUTO_INCREMENT值

在MySQL的客户端中你可以使用 SQL中的LAST_INSERT_ID( ) 函数来获取最后的插入表中的自增列的值。

在PHP或PERL脚本中也提供了相应的函数来获取最后的插入表中的自增列的值。

### PERL实例

使用 mysql_insertid 属性来获取 AUTO_INCREMENT 的值。 实例如下：

```mysql
$dbh->do ("INSERT INTO insect (name,date,origin)
VALUES('moth','2001-09-14','windowsill')");
my $seq = $dbh->{mysql_insertid};
```

### 重置序列

如果你删除了数据表中的多条记录，并希望对剩下数据的AUTO_INCREMENT列进行重新排列，那么你可以通过删除自增的列，然后重新添加来实现。 不过该操作要非常小心，如果在删除的同时又有新记录添加，有可能会出现数据混乱。操作如下所示：

```mysql
mysql> ALTER TABLE insect DROP id;
mysql> ALTER TABLE insect
    -> ADD id INT UNSIGNED NOT NULL AUTO_INCREMENT FIRST,
    -> ADD PRIMARY KEY (id);
```

------

### 设置序列的开始值

一般情况下序列的开始值为1，但如果你需要指定一个开始值100，那我们可以通过以下语句来实现：

```mysql
mysql> CREATE TABLE insect
    -> (
    -> id INT UNSIGNED NOT NULL AUTO_INCREMENT,
    -> PRIMARY KEY (id),
    -> name VARCHAR(30) NOT NULL, 
    -> date DATE NOT NULL,
    -> origin VARCHAR(30) NOT NULL
)engine=innodb auto_increment=100 charset=utf8;
```

或者你也可以在表创建成功后，通过以下语句来实现：

```mysql
mysql> ALTER TABLE t AUTO_INCREMENT = 100;
```



用函数创建自增序列管理表(批量使用自增表,设置初始值,自增幅度)

第一步：创建Sequence管理表 sequence

```mysql
DROP TABLE IF EXISTS sequence; 
CREATE TABLE sequence ( 
name VARCHAR(50) NOT NULL, 
current_value INT NOT NULL, 
increment INT NOT NULL DEFAULT 1, 
PRIMARY KEY (name) 
) ENGINE=InnoDB;
```

第二步：创建取当前值的函数 currval

```mysql
DROP FUNCTION IF EXISTS currval; 
DELIMITER $ 
CREATE FUNCTION currval (seq_name VARCHAR(50)) 
RETURNS INTEGER
LANGUAGE SQL 
DETERMINISTIC 
CONTAINS SQL 
SQL SECURITY DEFINER 
COMMENT ''
BEGIN
DECLARE value INTEGER; 
SET value = 0; 
SELECT current_value INTO value 
FROM sequence
WHERE name = seq_name; 
RETURN value; 
END
$ 
DELIMITER ;
```

第三步：创建取下一个值的函数 nextval

```mysql
DROP FUNCTION IF EXISTS nextval; 
DELIMITER $ 
CREATE FUNCTION nextval (seq_name VARCHAR(50)) 
RETURNS INTEGER
LANGUAGE SQL 
DETERMINISTIC 
CONTAINS SQL 
SQL SECURITY DEFINER 
COMMENT ''
BEGIN
UPDATE sequence
SET current_value = current_value + increment 
WHERE name = seq_name; 
RETURN currval(seq_name); 
END
$ 
DELIMITER;
```

第四步：创建更新当前值的函数 setval

```mysql
DROP FUNCTION IF EXISTS setval; 
DELIMITER $ 
CREATE FUNCTION setval (seq_name VARCHAR(50), value INTEGER) 
RETURNS INTEGER
LANGUAGE SQL 
DETERMINISTIC 
CONTAINS SQL 
SQL SECURITY DEFINER 
COMMENT ''
BEGIN
UPDATE sequence
SET current_value = value 
WHERE name = seq_name; 
RETURN currval(seq_name); 
END
$ 
DELIMITER ;
```

**测试函数功能**

当上述四步完成后，可以用以下数据设置需要创建的sequence名称以及设置初始值和获取当前值和下一个值。

```mysql
INSERT INTO sequence VALUES ('TestSeq', 0, 1);
----添加一个sequence名称和初始值，以及自增幅度  添加一个名为TestSeq 的自增序列

SELECT SETVAL('TestSeq', 10);
---设置指定sequence的初始值    这里设置TestSeq 的初始值为10

SELECT CURRVAL('TestSeq');  
--查询指定sequence的当前值   这里是获取TestSeq当前值

SELECT NEXTVAL('TestSeq');  
--查询指定sequence的下一个值  这里是获取TestSeq下一个值
```



## 处理重复数据

### 防止表中出现重复数据

你可以在 MySQL 数据表中设置指定的字段为 **PRIMARY KEY（主键）** 或者 **UNIQUE（唯一）** 索引来保证数据的唯一性。



让我们尝试一个实例：下表中无索引及主键，所以该表允许出现多条重复记录。

```mysql
CREATE TABLE person_tbl
(
    first_name CHAR(20),
    last_name CHAR(20),
    sex CHAR(10)
);
```

如果你想设置表中字段 first_name，last_name 数据不能重复，你可以设置双主键模式来设置数据的唯一性， 如果你设置了双主键，那么那个键的默认值不能为 NULL，可设置为 NOT NULL。如下所示：

```mysql
CREATE TABLE person_tbl
(
   first_name CHAR(20) NOT NULL,
   last_name CHAR(20) NOT NULL,
   sex CHAR(10),
   PRIMARY KEY (last_name, first_name)
);
```

如果我们设置了唯一索引，那么在插入重复数据时，SQL 语句将无法执行成功,并抛出错。

INSERT IGNORE INTO 与 INSERT INTO 的区别就是 INSERT IGNORE 会忽略数据库中已经存在的数据，如果数据库没有数据，就插入新的数据，如果有数据的话就跳过这条数据。这样就可以保留数据库中已经存在数据，达到在间隙中插入数据的目的。

以下实例使用了 INSERT IGNORE INTO，执行后不会出错，也不会向数据表中插入重复数据：

```mysql
mysql> INSERT IGNORE INTO person_tbl (last_name, first_name)
    -> VALUES( 'Jay', 'Thomas');
Query OK, 1 row affected (0.00 sec)
mysql> INSERT IGNORE INTO person_tbl (last_name, first_name)
    -> VALUES( 'Jay', 'Thomas');
Query OK, 0 rows affected (0.00 sec)
```

INSERT IGNORE INTO 当插入数据时，在设置了记录的唯一性后，如果插入重复数据，将不返回错误，只以警告形式返回。 而 REPLACE INTO 如果存在 primary 或 unique 相同的记录，则先删除掉。再插入新记录。

另一种设置数据的唯一性方法是添加一个 UNIQUE 索引，如下所示：

```mysql
CREATE TABLE person_tbl
(
   first_name CHAR(20) NOT NULL,
   last_name CHAR(20) NOT NULL,
   sex CHAR(10),
   UNIQUE (last_name, first_name)
);
```

------

### 统计重复数据

以下我们将统计表中 first_name 和 last_name的重复记录数：

```mysql
mysql> SELECT COUNT(*) as repetitions, last_name, first_name
    -> FROM person_tbl
    -> GROUP BY last_name, first_name
    -> HAVING repetitions > 1;
```

以上查询语句将返回 person_tbl 表中重复的记录数。 一般情况下，查询重复的值，请执行以下操作：

- 确定哪一列包含的值可能会重复。
- 在列选择列表使用COUNT(*)列出的那些列。
- 在GROUP BY子句中列出的列。
- HAVING子句设置重复数大于1。

------

### 过滤重复数据

如果你需要读取不重复的数据可以在 SELECT 语句中使用 DISTINCT 关键字来过滤重复数据。

```mysql
mysql> SELECT DISTINCT last_name, first_name
    -> FROM person_tbl;
```

你也可以使用 GROUP BY 来读取数据表中不重复的数据：

```mysql
mysql> SELECT last_name, first_name
    -> FROM person_tbl
    -> GROUP BY (last_name, first_name);
```

------

### 删除重复数据

如果你想删除数据表中的重复数据，你可以使用以下的SQL语句：

```mysql
mysql> CREATE TABLE tmp SELECT last_name, first_name, sex FROM person_tbl  GROUP BY (last_name, first_name, sex);
mysql> DROP TABLE person_tbl;
mysql> ALTER TABLE tmp RENAME TO person_tbl;
```

当然你也可以在数据表中添加 INDEX（索引） 和 PRIMAY KEY（主键）这种简单的方法来删除表中的重复记录。方法如下：

```mysql
mysql> ALTER IGNORE TABLE person_tbl
    -> ADD PRIMARY KEY (last_name, first_name);
```



### 笔记

```mysql
select 列名1，count（1） as count 
from 表名
group by  列名1
having count>1  and 其他条件

select 列名1，列名2，count（1） as count 
from 表名
group by  列名1，列名2 
having count>1  and 其他条件
```

原理：先按照要查询出现重复数据的列，进行分组查询。count > 1 代表出现 2 次或 2 次以上。

示例：

```mysql
/*查询重复数据*/
select serialnum,cdate,count(*) as count 
from m_8_customer_temp_20180820bak 
group by serialnum,cdate having  count>1 and cdate>='2018-08-20 00:00:00';
```





## SQL 注入

所谓SQL注入，就是通过把SQL命令插入到Web表单递交或输入域名或页面请求的查询字符串，最终达到欺骗服务器执行恶意的SQL命令。

我们永远不要信任用户的输入，我们必须认定用户输入的数据都是不安全的，我们都需要对用户输入的数据进行过滤处理。

### 防止SQL注入

在脚本语言，如Perl和PHP你可以对用户输入的数据进行转义从而来防止SQL注入。

PHP的MySQL扩展提供了mysqli_real_escape_string()函数来转义特殊的输入字符。

```mysql
if (get_magic_quotes_gpc()) 
{
  $name = stripslashes($name);
}
$name = mysqli_real_escape_string($conn, $name);
 mysqli_query($conn, "SELECT * FROM users WHERE name='{$name}'");
```

------

### Like语句中的注入

like查询时，如果用户输入的值有"_"和"%"，则会出现这种情况：用户本来只是想查询"abcd_"，查询结果中却有"abcd_"、"abcde"、"abcdf"等等；用户要查询"30%"（注：百分之三十）时也会出现问题。

在PHP脚本中我们可以使用addcslashes()函数来处理以上情况，如下实例：

```mysql
$sub = addcslashes(mysqli_real_escape_string($conn, "%something_"), "%_");
// $sub == \%something\_
 mysqli_query($conn, "SELECT * FROM messages WHERE subject LIKE '{$sub}%'");
```

addcslashes() 函数在指定的字符前添加反斜杠。

语法格式:

```mysql
addcslashes(string,characters)
```

| 参数       | 描述                                              |
| :--------- | :------------------------------------------------ |
| string     | 必需。规定要检查的字符串。                        |
| characters | 可选。规定受 addcslashes() 影响的字符或字符范围。 |



## 导出数据



### SELECT ... INTO OUTFILE 语句导出数据



以下实例中我们将数据表 runoob_tbl 数据导出到 /tmp/runoob.txt 文件中:

```mysql
mysql> SELECT * FROM runoob_tbl 
    -> INTO OUTFILE '/tmp/runoob.txt';
```

你可以通过命令选项来设置数据输出的指定格式，以下实例为导出 CSV 格式：

```mysql
mysql> SELECT * FROM passwd INTO OUTFILE '/tmp/runoob.txt'
    -> FIELDS TERMINATED BY ',' ENCLOSED BY '"'
    -> LINES TERMINATED BY '\r\n';
```

在下面的例子中，生成一个文件，各值用逗号隔开。这种格式可以被许多程序使用。

```mysql
SELECT a,b,a+b INTO OUTFILE '/tmp/result.text'
FIELDS TERMINATED BY ',' OPTIONALLY ENCLOSED BY '"'
LINES TERMINATED BY '\n'
FROM test_table;
```

### SELECT ... INTO OUTFILE 语句有以下属性:

- LOAD DATA INFILE是SELECT ... INTO OUTFILE的逆操作，SELECT句法。为了将一个数据库的数据写入一个文件，使用SELECT ... INTO OUTFILE，为了将文件读回数据库，使用LOAD DATA INFILE。
- SELECT...INTO OUTFILE 'file_name'形式的SELECT可以把被选择的行写入一个文件中。该文件被创建到服务器主机上，因此您必须拥有FILE权限，才能使用此语法。
- 输出不能是一个已存在的文件。防止文件数据被篡改。
- 你需要有一个登陆服务器的账号来检索文件。否则 SELECT ... INTO OUTFILE 不会起任何作用。
- 在UNIX中，该文件被创建后是可读的，权限由MySQL服务器所拥有。这意味着，虽然你就可以读取该文件，但可能无法将其删除。

------

### 导出表作为原始数据

**mysqldump** 是 mysql 用于转存储数据库的实用程序。它主要产生一个 SQL 脚本，其中包含从头重新创建数据库所必需的命令 CREATE TABLE INSERT 等。

使用 **mysqldump** 导出数据需要使用 **--tab** 选项来指定导出文件指定的目录，该目标必须是可写的。

以下实例将数据表 runoob_tbl 导出到 /tmp 目录中：

```mysql
$ mysqldump -u root -p --no-create-info \
            --tab=/tmp RUNOOB runoob_tbl
password ******
```

------

### 导出 SQL 格式的数据

导出 SQL 格式的数据到指定文件，如下所示：

```mysql
$ mysqldump -u root -p RUNOOB runoob_tbl > dump.txt
password ******
```

如果你需要导出整个数据库的数据，可以使用以下命令：

```mysql
$ mysqldump -u root -p RUNOOB > database_dump.txt
password ******
```

如果需要备份所有数据库，可以使用以下命令：

```mysql
$ mysqldump -u root -p --all-databases > database_dump.txt
password ******
```



### 将数据表及数据库拷贝至其他主机

如果你需要将数据拷贝至其他的 MySQL 服务器上, 你可以在 mysqldump 命令中指定数据库名及数据表。

在源主机上执行以下命令，将数据备份至 dump.txt 文件中:

```mysql
$ mysqldump -u root -p database_name table_name > dump.txt
password *****
```

如果完整备份数据库，则无需使用特定的表名称。

如果你需要将备份的数据库导入到MySQL服务器中，可以使用以下命令，使用以下命令你需要确认数据库已经创建：

```mysql
$ mysql -u root -p database_name < dump.txt
password *****
```

你也可以使用以下命令将导出的数据直接导入到远程的服务器上，但请确保两台服务器是相通的，是可以相互访问的：

```mysql
$ mysqldump -u root -p database_name \
       | mysql -h other-host.com database_name
```

以上命令中使用了管道来将导出的数据导入到指定的远程主机上。



### 将指定主机的数据库拷贝到本地

如果你需要将远程服务器的数据拷贝到本地，你也可以在 mysqldump 命令中指定远程服务器的IP、端口及数据库名。

在源主机上执行以下命令，将数据备份到 dump.txt 文件中：

请确保两台服务器是相通的：

```mysql
mysqldump -h other-host.com -P port -u root -p database_name > dump.txt
password ****
```

## 导入数据

### 1、mysql 命令导入

使用 mysql 命令导入语法格式为：

```mysql
mysql -u用户名    -p密码    <  要导入的数据库数据(runoob.sql)
```

**实例：**

```mysql
# mysql -uroot -p123456 < runoob.sql
```

以上命令将将备份的整个数据库 runoob.sql 导入。

------

### 2、source 命令导入

source 命令导入数据库需要先登录到数库终端：

```
mysql> create database abc;      # 创建数据库
mysql> use abc;                  # 使用已创建的数据库 
mysql> set names utf8;           # 设置编码
mysql> source /home/abc/abc.sql  # 导入备份数据库
```

------

### 3、使用 LOAD DATA 导入数据

MySQL 中提供了LOAD DATA INFILE语句来插入数据。 以下实例中将从当前目录中读取文件 dump.txt ，将该文件中的数据插入到当前数据库的 mytbl 表中。

```mysql
mysql> LOAD DATA LOCAL INFILE 'dump.txt' INTO TABLE mytbl;
```

　如果指定LOCAL关键词，则表明从客户主机上按路径读取文件。如果没有指定，则文件在服务器上按路径读取文件。

你能明确地在LOAD DATA语句中指出列值的分隔符和行尾标记，但是默认标记是定位符和换行符。

两个命令的 FIELDS 和 LINES 子句的语法是一样的。两个子句都是可选的，但是如果两个同时被指定，FIELDS 子句必须出现在 LINES 子句之前。

如果用户指定一个 FIELDS 子句，它的子句 （TERMINATED BY、[OPTIONALLY] ENCLOSED BY 和 ESCAPED BY) 也是可选的，不过，用户必须至少指定它们中的一个。

```mysql
mysql> LOAD DATA LOCAL INFILE 'dump.txt' INTO TABLE mytbl
  -> FIELDS TERMINATED BY ':'
  -> LINES TERMINATED BY '\r\n';
```

LOAD DATA 默认情况下是按照数据文件中列的顺序插入数据的，如果数据文件中的列与插入表中的列不一致，则需要指定列的顺序。

如，在数据文件中的列顺序是 a,b,c，但在插入表的列顺序为b,c,a，则数据导入语法如下：

```mysql
mysql> LOAD DATA LOCAL INFILE 'dump.txt' 
    -> INTO TABLE mytbl (b, c, a);
```

------

### 4、使用 mysqlimport 导入数据

mysqlimport 客户端提供了 LOAD DATA INFILEQL 语句的一个命令行接口。mysqlimport 的大多数选项直接对应 LOAD DATA INFILE 子句。

从文件 dump.txt 中将数据导入到 mytbl 数据表中, 可以使用以下命令：

```mysql
$ mysqlimport -u root -p --local mytbl dump.txt
password *****
```

mysqlimport 命令可以指定选项来设置指定格式,命令语句格式如下：

```mysql
$ mysqlimport -u root -p --local --fields-terminated-by=":" \
   --lines-terminated-by="\r\n"  mytbl dump.txt
password *****
```

mysqlimport 语句中使用 --columns 选项来设置列的顺序：

```mysql
$ mysqlimport -u root -p --local --columns=b,c,a \
    mytbl dump.txt
password *****
```

------

### mysqlimport的常用选项介绍

| 选项                         | 功能                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| -d or --delete               | 新数据导入数据表中之前删除数据数据表中的所有信息             |
| -f or --force                | 不管是否遇到错误，mysqlimport将强制继续插入数据              |
| -i or --ignore               | mysqlimport跳过或者忽略那些有相同唯一 关键字的行， 导入文件中的数据将被忽略。 |
| -l or -lock-tables           | 数据被插入之前锁住表，这样就防止了， 你在更新数据库时，用户的查询和更新受到影响。 |
| -r or -replace               | 这个选项与－i选项的作用相反；此选项将替代 表中有相同唯一关键字的记录。 |
| --fields-enclosed- by= char  | 指定文本文件中数据的记录时以什么括起的， 很多情况下 数据以双引号括起。 默认的情况下数据是没有被字符括起的。 |
| --fields-terminated- by=char | 指定各个数据的值之间的分隔符，在句号分隔的文件中， 分隔符是句号。您可以用此选项指定数据之间的分隔符。 默认的分隔符是跳格符（Tab） |
| --lines-terminated- by=str   | 此选项指定文本文件中行与行之间数据的分隔字符串 或者字符。 默认的情况下mysqlimport以newline为行分隔符。 您可以选择用一个字符串来替代一个单个的字符： 一个新行或者一个回车。 |

mysqlimport 命令常用的选项还有 -v 显示版本（version）， -p 提示输入密码（password）等。



## MySQL 函数

MySQL 有很多内置的函数，以下列出了这些函数的说明。

------

### MySQL 字符串函数

| 函数                                  | 描述                                                         | 实例                                                         |
| :------------------------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| ASCII(s)                              | 返回字符串 s 的第一个字符的 ASCII 码。                       | 返回 CustomerName 字段第一个字母的 ASCII 码：`SELECT ASCII(CustomerName) AS NumCodeOfFirstChar FROM Customers;` |
| CHAR_LENGTH(s)                        | 返回字符串 s 的字符数                                        | 返回字符串 RUNOOB 的字符数`SELECT CHAR_LENGTH("RUNOOB") AS LengthOfString;` |
| CHARACTER_LENGTH(s)                   | 返回字符串 s 的字符数                                        | 返回字符串 RUNOOB 的字符数`SELECT CHARACTER_LENGTH("RUNOOB") AS LengthOfString;` |
| CONCAT(s1,s2...sn)                    | 字符串 s1,s2 等多个字符串合并为一个字符串                    | 合并多个字符串`SELECT CONCAT("SQL ", "Runoob ", "Gooogle ", "Facebook") AS ConcatenatedString;` |
| CONCAT_WS(x, s1,s2...sn)              | 同 CONCAT(s1,s2,...) 函数，但是每个字符串之间要加上 x，x 可以是分隔符 | 合并多个字符串，并添加分隔符：`SELECT CONCAT_WS("-", "SQL", "Tutorial", "is", "fun!")AS ConcatenatedString;` |
| FIELD(s,s1,s2...)                     | 返回第一个字符串 s 在字符串列表(s1,s2...)中的位置            | 返回字符串 c 在列表值中的位置：`SELECT FIELD("c", "a", "b", "c", "d", "e");` |
| FIND_IN_SET(s1,s2)                    | 返回在字符串s2中与s1匹配的字符串的位置                       | 返回字符串 c 在指定字符串中的位置：`SELECT FIND_IN_SET("c", "a,b,c,d,e");` |
| FORMAT(x,n)                           | 函数可以将数字 x 进行格式化 "#,###.##", 将 x 保留到小数点后 n 位，最后一位四舍五入。 | 格式化数字 "#,###.##" 形式：`SELECT FORMAT(250500.5634, 2);     -- 输出 250,500.56` |
| INSERT(s1,x,len,s2)                   | 字符串 s2 替换 s1 的 x 位置开始长度为 len 的字符串           | 从字符串第一个位置开始的 6 个字符替换为 runoob：`SELECT INSERT("google.com", 1, 6, "runnob");  -- 输出：runoob.com` |
| LOCATE(s1,s)                          | 从字符串 s 中获取 s1 的开始位置                              | 获取 b 在字符串 abc 中的位置：`SELECT LOCATE('st','myteststring');  -- 5`返回字符串 abc 中 b 的位置：`SELECT LOCATE('b', 'abc') -- 2` |
| LCASE(s)                              | 将字符串 s 的所有字母变成小写字母                            | 字符串 RUNOOB 转换为小写：`SELECT LCASE('RUNOOB') -- runoob` |
| LEFT(s,n)                             | 返回字符串 s 的前 n 个字符                                   | 返回字符串 runoob 中的前两个字符：`SELECT LEFT('runoob',2) -- ru` |
| LOWER(s)                              | 将字符串 s 的所有字母变成小写字母                            | 字符串 RUNOOB 转换为小写：`SELECT LOWER('RUNOOB') -- runoob` |
| LPAD(s1,len,s2)                       | 在字符串 s1 的开始处填充字符串 s2，使字符串长度达到 len      | 将字符串 xx 填充到 abc 字符串的开始处：`SELECT LPAD('abc',5,'xx') -- xxabc` |
| LTRIM(s)                              | 去掉字符串 s 开始处的空格                                    | 去掉字符串 RUNOOB开始处的空格：`SELECT LTRIM("    RUNOOB") AS LeftTrimmedString;-- RUNOOB` |
| MID(s,n,len)                          | 从字符串 s 的 n 位置截取长度为 len 的子字符串，同 SUBSTRING(s,n,len) | 从字符串 RUNOOB 中的第 2 个位置截取 3个 字符：`SELECT MID("RUNOOB", 2, 3) AS ExtractString; -- UNO` |
| POSITION(s1 IN s)                     | 从字符串 s 中获取 s1 的开始位置                              | 返回字符串 abc 中 b 的位置：`SELECT POSITION('b' in 'abc') -- 2` |
| REPEAT(s,n)                           | 将字符串 s 重复 n 次                                         | 将字符串 runoob 重复三次：`SELECT REPEAT('runoob',3) -- runoobrunoobrunoob` |
| REPLACE(s,s1,s2)                      | 将字符串 s2 替代字符串 s 中的字符串 s1                       | 将字符串 abc 中的字符 a 替换为字符 x：`SELECT REPLACE('abc','a','x') --xbc` |
| REVERSE(s)                            | 将字符串s的顺序反过来                                        | 将字符串 abc 的顺序反过来：`SELECT REVERSE('abc') -- cba`    |
| RIGHT(s,n)                            | 返回字符串 s 的后 n 个字符                                   | 返回字符串 runoob 的后两个字符：`SELECT RIGHT('runoob',2) -- ob` |
| RPAD(s1,len,s2)                       | 在字符串 s1 的结尾处添加字符串 s2，使字符串的长度达到 len    | 将字符串 xx 填充到 abc 字符串的结尾处：`SELECT RPAD('abc',5,'xx') -- abcxx` |
| RTRIM(s)                              | 去掉字符串 s 结尾处的空格                                    | 去掉字符串 RUNOOB 的末尾空格：`SELECT RTRIM("RUNOOB     ") AS RightTrimmedString;   -- RUNOOB` |
| SPACE(n)                              | 返回 n 个空格                                                | 返回 10 个空格：`SELECT SPACE(10);`                          |
| STRCMP(s1,s2)                         | 比较字符串 s1 和 s2，如果 s1 与 s2 相等返回 0 ，如果 s1>s2 返回 1，如果 s1<s2 返回 -1 | 比较字符串：`SELECT STRCMP("runoob", "runoob");  -- 0`       |
| SUBSTR(s, start, length)              | 从字符串 s 的 start 位置截取长度为 length 的子字符串         | 从字符串 RUNOOB 中的第 2 个位置截取 3个 字符：`SELECT SUBSTR("RUNOOB", 2, 3) AS ExtractString; -- UNO` |
| SUBSTRING(s, start, length)           | 从字符串 s 的 start 位置截取长度为 length 的子字符串         | 从字符串 RUNOOB 中的第 2 个位置截取 3个 字符：`SELECT SUBSTRING("RUNOOB", 2, 3) AS ExtractString; -- UNO` |
| SUBSTRING_INDEX(s, delimiter, number) | 返回从字符串 s 的第 number 个出现的分隔符 delimiter 之后的子串。 如果 number 是正数，返回第 number 个字符左边的字符串。 如果 number 是负数，返回第(number 的绝对值(从右边数))个字符右边的字符串。 | `SELECT SUBSTRING_INDEX('a*b','*',1) -- a SELECT SUBSTRING_INDEX('a*b','*',-1)  -- b SELECT SUBSTRING_INDEX(SUBSTRING_INDEX('a*b*c*d*e','*',3),'*',-1)  -- c` |
| TRIM(s)                               | 去掉字符串 s 开始和结尾处的空格                              | 去掉字符串 RUNOOB 的首尾空格：`SELECT TRIM('    RUNOOB    ') AS TrimmedString;` |
| UCASE(s)                              | 将字符串转换为大写                                           | 将字符串 runoob 转换为大写：`SELECT UCASE("runoob"); -- RUNOOB` |
| UPPER(s)                              | 将字符串转换为大写                                           | 将字符串 runoob 转换为大写：`SELECT UPPER("runoob"); -- RUNOOB` |

------

### MySQL 数字函数

| 函数名                                               | 描述                                                         | 实例                                                         |
| :--------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| ABS(x)                                               | 返回 x 的绝对值                                              | 返回 -1 的绝对值：`SELECT ABS(-1) -- 返回1`                  |
| ACOS(x)                                              | 求 x 的反余弦值(参数是弧度)                                  | `SELECT ACOS(0.25);`                                         |
| ASIN(x)                                              | 求反正弦值(参数是弧度)                                       | `SELECT ASIN(0.25);`                                         |
| ATAN(x)                                              | 求反正切值(参数是弧度)                                       | `SELECT ATAN(2.5);`                                          |
| ATAN2(n, m)                                          | 求反正切值(参数是弧度)                                       | `SELECT ATAN2(-0.8, 2);`                                     |
| AVG(expression)                                      | 返回一个表达式的平均值，expression 是一个字段                | 返回 Products 表中Price 字段的平均值：`SELECT AVG(Price) AS AveragePrice FROM Products;` |
| CEIL(x)                                              | 返回大于或等于 x 的最小整数                                  | `SELECT CEIL(1.5) -- 返回2`                                  |
| CEILING(x)                                           | 返回大于或等于 x 的最小整数                                  | `SELECT CEIL(1.5) -- 返回2`                                  |
| COS(x)                                               | 求余弦值(参数是弧度)                                         | `SELECT COS(2);`                                             |
| COT(x)                                               | 求余切值(参数是弧度)                                         | `SELECT COT(6);`                                             |
| COUNT(expression)                                    | 返回查询的记录总数，expression 参数是一个字段或者 * 号       | 返回 Products 表中 products 字段总共有多少条记录：`SELECT COUNT(ProductID) AS NumberOfProducts FROM Products;` |
| DEGREES(x)                                           | 将弧度转换为角度                                             | `SELECT DEGREES(3.1415926535898) -- 180`                     |
| n DIV m                                              | 整除，n 为被除数，m 为除数                                   | 计算 10 除于 5：`SELECT 10 DIV 5;  -- 2`                     |
| EXP(x)                                               | 返回 e 的 x 次方                                             | 计算 e 的三次方：`SELECT EXP(3) -- 20.085536923188`          |
| FLOOR(x)                                             | 返回小于或等于 x 的最大整数                                  | 小于或等于 1.5 的整数：`SELECT FLOOR(1.5) -- 返回1`          |
| GREATEST(expr1, expr2, expr3, ...)                   | 返回列表中的最大值                                           | 返回以下数字列表中的最大值：`SELECT GREATEST(3, 12, 34, 8, 25); -- 34`返回以下字符串列表中的最大值：`SELECT GREATEST("Google", "Runoob", "Apple");   -- Runoob` |
| LEAST(expr1, expr2, expr3, ...)                      | 返回列表中的最小值                                           | 返回以下数字列表中的最小值：`SELECT LEAST(3, 12, 34, 8, 25); -- 3`返回以下字符串列表中的最小值：`SELECT LEAST("Google", "Runoob", "Apple");   -- Apple` |
| [LN](https://www.runoob.com/mysql/func_mysql_ln.asp) | 返回数字的自然对数                                           | 返回 2 的自然对数：`SELECT LN(2);  -- 0.6931471805599453`    |
| LOG(x)                                               | 返回自然对数(以 e 为底的对数)                                | `SELECT LOG(20.085536923188) -- 3`                           |
| LOG10(x)                                             | 返回以 10 为底的对数                                         | `SELECT LOG10(100) -- 2`                                     |
| LOG2(x)                                              | 返回以 2 为底的对数                                          | 返回以 2 为底 6 的对数：`SELECT LOG2(6);  -- 2.584962500721156` |
| MAX(expression)                                      | 返回字段 expression 中的最大值                               | 返回数据表 Products 中字段 Price 的最大值：`SELECT MAX(Price) AS LargestPrice FROM Products;` |
| MIN(expression)                                      | 返回字段 expression 中的最小值                               | 返回数据表 Products 中字段 Price 的最小值：`SELECT MIN(Price) AS LargestPrice FROM Products;` |
| MOD(x,y)                                             | 返回 x 除以 y 以后的余数                                     | 5 除于 2 的余数：`SELECT MOD(5,2) -- 1`                      |
| PI()                                                 | 返回圆周率(3.141593）                                        | `SELECT PI() --3.141593`                                     |
| POW(x,y)                                             | 返回 x 的 y 次方                                             | 2 的 3 次方：`SELECT POW(2,3) -- 8`                          |
| POWER(x,y)                                           | 返回 x 的 y 次方                                             | 2 的 3 次方：`SELECT POWER(2,3) -- 8`                        |
| RADIANS(x)                                           | 将角度转换为弧度                                             | 180 度转换为弧度：`SELECT RADIANS(180) -- 3.1415926535898`   |
| RAND()                                               | 返回 0 到 1 的随机数                                         | `SELECT RAND() --0.93099315644334`                           |
| ROUND(x)                                             | 返回离 x 最近的整数                                          | `SELECT ROUND(1.23456) --1`                                  |
| SIGN(x)                                              | 返回 x 的符号，x 是负数、0、正数分别返回 -1、0 和 1          | `SELECT SIGN(-10) -- (-1)`                                   |
| SIN(x)                                               | 求正弦值(参数是弧度)                                         | `SELECT SIN(RADIANS(30)) -- 0.5`                             |
| SQRT(x)                                              | 返回x的平方根                                                | 25 的平方根：`SELECT SQRT(25) -- 5`                          |
| SUM(expression)                                      | 返回指定字段的总和                                           | 计算 OrderDetails 表中字段 Quantity 的总和：`SELECT SUM(Quantity) AS TotalItemsOrdered FROM OrderDetails;` |
| TAN(x)                                               | 求正切值(参数是弧度)                                         | `SELECT TAN(1.75);  -- -5.52037992250933`                    |
| TRUNCATE(x,y)                                        | 返回数值 x 保留到小数点后 y 位的值（与 ROUND 最大的区别是不会进行四舍五入） | `SELECT TRUNCATE(1.23456,3) -- 1.234`                        |

------

### MySQL 日期函数

| 函数名                            | 描述                                                         | 实例                                                         |
| :-------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| ADDDATE(d,n)                      | 计算起始日期 d 加上 n 天的日期                               | `SELECT ADDDATE("2017-06-15", INTERVAL 10 DAY); ->2017-06-25` |
| ADDTIME(t,n)                      | 时间 t 加上 n 秒的时间                                       | `SELECT ADDTIME('2011-11-11 11:11:11', 5) ->2011-11-11 11:11:16 (秒)` |
| CURDATE()                         | 返回当前日期                                                 | `SELECT CURDATE(); -> 2018-09-19`                            |
| CURRENT_DATE()                    | 返回当前日期                                                 | `SELECT CURRENT_DATE(); -> 2018-09-19`                       |
| CURRENT_TIME                      | 返回当前时间                                                 | `SELECT CURRENT_TIME(); -> 19:59:02`                         |
| CURRENT_TIMESTAMP()               | 返回当前日期和时间                                           | `SELECT CURRENT_TIMESTAMP() -> 2018-09-19 20:57:43`          |
| CURTIME()                         | 返回当前时间                                                 | `SELECT CURTIME(); -> 19:59:02`                              |
| DATE()                            | 从日期或日期时间表达式中提取日期值                           | `SELECT DATE("2017-06-15");     -> 2017-06-15`               |
| DATEDIFF(d1,d2)                   | 计算日期 d1->d2 之间相隔的天数                               | `SELECT DATEDIFF('2001-01-01','2001-02-02') -> -32`          |
| DATE_ADD(d，INTERVAL expr type)   | 计算起始日期 d 加上一个时间段后的日期                        | `SELECT ADDDATE('2011-11-11 11:11:11',1) -> 2011-11-12 11:11:11  (默认是天) SELECT ADDDATE('2011-11-11 11:11:11', INTERVAL 5 MINUTE) -> 2011-11-11 11:16:11 (TYPE的取值与上面那个列出来的函数类似)` |
| DATE_FORMAT(d,f)                  | 按表达式 f的要求显示日期 d                                   | `SELECT DATE_FORMAT('2011-11-11 11:11:11','%Y-%m-%d %r') -> 2011-11-11 11:11:11 AM` |
| DATE_SUB(date,INTERVAL expr type) | 函数从日期减去指定的时间间隔。                               | Orders 表中 OrderDate 字段减去 2 天：`SELECT OrderId,DATE_SUB(OrderDate,INTERVAL 2 DAY) AS OrderPayDate FROM Orders` |
| DAY(d)                            | 返回日期值 d 的日期部分                                      | `SELECT DAY("2017-06-15");   -> 15`                          |
| DAYNAME(d)                        | 返回日期 d 是星期几，如 Monday,Tuesday                       | `SELECT DAYNAME('2011-11-11 11:11:11') ->Friday`             |
| DAYOFMONTH(d)                     | 计算日期 d 是本月的第几天                                    | `SELECT DAYOFMONTH('2011-11-11 11:11:11') ->11`              |
| DAYOFWEEK(d)                      | 日期 d 今天是星期几，1 星期日，2 星期一，以此类推            | `SELECT DAYOFWEEK('2011-11-11 11:11:11') ->6`                |
| DAYOFYEAR(d)                      | 计算日期 d 是本年的第几天                                    | `SELECT DAYOFYEAR('2011-11-11 11:11:11') ->315`              |
| EXTRACT(type FROM d)              | 从日期 d 中获取指定的值，type 指定返回的值。 type可取值为： MICROSECONDSECONDMINUTEHOURDAYWEEKMONTHQUARTERYEARSECOND_MICROSECONDMINUTE_MICROSECONDMINUTE_SECONDHOUR_MICROSECONDHOUR_SECONDHOUR_MINUTEDAY_MICROSECONDDAY_SECONDDAY_MINUTEDAY_HOURYEAR_MONTH | `SELECT EXTRACT(MINUTE FROM '2011-11-11 11:11:11')  -> 11`   |
| FROM_DAYS(n)                      | 计算从 0000 年 1 月 1 日开始 n 天后的日期                    | `SELECT FROM_DAYS(1111) -> 0003-01-16`                       |
| HOUR(t)                           | 返回 t 中的小时值                                            | `SELECT HOUR('1:2:3') -> 1`                                  |
| LAST_DAY(d)                       | 返回给给定日期的那一月份的最后一天                           | `SELECT LAST_DAY("2017-06-20"); -> 2017-06-30`               |
| LOCALTIME()                       | 返回当前日期和时间                                           | `SELECT LOCALTIME() -> 2018-09-19 20:57:43`                  |
| LOCALTIMESTAMP()                  | 返回当前日期和时间                                           | `SELECT LOCALTIMESTAMP() -> 2018-09-19 20:57:43`             |
| MAKEDATE(year, day-of-year)       | 基于给定参数年份 year 和所在年中的天数序号 day-of-year 返回一个日期 | `SELECT MAKEDATE(2017, 3); -> 2017-01-03`                    |
| MAKETIME(hour, minute, second)    | 组合时间，参数分别为小时、分钟、秒                           | `SELECT MAKETIME(11, 35, 4); -> 11:35:04`                    |
| MICROSECOND(date)                 | 返回日期参数所对应的微秒数                                   | `SELECT MICROSECOND("2017-06-20 09:34:00.000023"); -> 23`    |
| MINUTE(t)                         | 返回 t 中的分钟值                                            | `SELECT MINUTE('1:2:3') -> 2`                                |
| MONTHNAME(d)                      | 返回日期当中的月份名称，如 November                          | `SELECT MONTHNAME('2011-11-11 11:11:11') -> November`        |
| MONTH(d)                          | 返回日期d中的月份值，1 到 12                                 | `SELECT MONTH('2011-11-11 11:11:11') ->11`                   |
| NOW()                             | 返回当前日期和时间                                           | `SELECT NOW() -> 2018-09-19 20:57:43`                        |
| PERIOD_ADD(period, number)        | 为 年-月 组合日期添加一个时段                                | `SELECT PERIOD_ADD(201703, 5);    -> 201708`                 |
| PERIOD_DIFF(period1, period2)     | 返回两个时段之间的月份差值                                   | `SELECT PERIOD_DIFF(201710, 201703); -> 7`                   |
| QUARTER(d)                        | 返回日期d是第几季节，返回 1 到 4                             | `SELECT QUARTER('2011-11-11 11:11:11') -> 4`                 |
| SECOND(t)                         | 返回 t 中的秒钟值                                            | `SELECT SECOND('1:2:3') -> 3`                                |
| SEC_TO_TIME(s)                    | 将以秒为单位的时间 s 转换为时分秒的格式                      | `SELECT SEC_TO_TIME(4320) -> 01:12:00`                       |
| STR_TO_DATE(string, format_mask)  | 将字符串转变为日期                                           | `SELECT STR_TO_DATE("August 10 2017", "%M %d %Y"); -> 2017-08-10` |
| SUBDATE(d,n)                      | 日期 d 减去 n 天后的日期                                     | `SELECT SUBDATE('2011-11-11 11:11:11', 1) ->2011-11-10 11:11:11 (默认是天)` |
| SUBTIME(t,n)                      | 时间 t 减去 n 秒的时间                                       | `SELECT SUBTIME('2011-11-11 11:11:11', 5) ->2011-11-11 11:11:06 (秒)` |
| SYSDATE()                         | 返回当前日期和时间                                           | `SELECT SYSDATE() -> 2018-09-19 20:57:43`                    |
| TIME(expression)                  | 提取传入表达式的时间部分                                     | `SELECT TIME("19:30:10"); -> 19:30:10`                       |
| TIME_FORMAT(t,f)                  | 按表达式 f 的要求显示时间 t                                  | `SELECT TIME_FORMAT('11:11:11','%r') 11:11:11 AM`            |
| TIME_TO_SEC(t)                    | 将时间 t 转换为秒                                            | `SELECT TIME_TO_SEC('1:12:00') -> 4320`                      |
| TIMEDIFF(time1, time2)            | 计算时间差值                                                 | `SELECT TIMEDIFF("13:10:11", "13:10:10"); -> 00:00:01`       |
| TIMESTAMP(expression, interval)   | 单个参数时，函数返回日期或日期时间表达式；有2个参数时，将参数加和 | `SELECT TIMESTAMP("2017-07-23",  "13:10:11"); -> 2017-07-23 13:10:11` |
| TO_DAYS(d)                        | 计算日期 d 距离 0000 年 1 月 1 日的天数                      | `SELECT TO_DAYS('0001-01-01 01:01:01') -> 366`               |
| WEEK(d)                           | 计算日期 d 是本年的第几个星期，范围是 0 到 53                | `SELECT WEEK('2011-11-11 11:11:11') -> 45`                   |
| WEEKDAY(d)                        | 日期 d 是星期几，0 表示星期一，1 表示星期二                  | `SELECT WEEKDAY("2017-06-15"); -> 3`                         |
| WEEKOFYEAR(d)                     | 计算日期 d 是本年的第几个星期，范围是 0 到 53                | `SELECT WEEKOFYEAR('2011-11-11 11:11:11') -> 45`             |
| YEAR(d)                           | 返回年份                                                     | `SELECT YEAR("2017-06-15"); -> 2017`                         |
| YEARWEEK(date, mode)              | 返回年份及第几周（0到53），mode 中 0 表示周天，1表示周一，以此类推 | `SELECT YEARWEEK("2017-06-15"); -> 201724`                   |

------

### MySQL 高级函数

| 函数名                                                       | 描述                                                         | 实例                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| BIN(x)                                                       | 返回 x 的二进制编码                                          | 15 的 2 进制编码:`SELECT BIN(15); -- 1111`                   |
| BINARY(s)                                                    | 将字符串 s 转换为二进制字符串                                | `SELECT BINARY "RUNOOB"; -> RUNOOB`                          |
| `CASE expression    WHEN condition1 THEN result1    WHEN condition2 THEN result2   ...    WHEN conditionN THEN resultN    ELSE result END` | CASE 表示函数开始，END 表示函数结束。如果 condition1 成立，则返回 result1, 如果 condition2 成立，则返回 result2，当全部不成立则返回 result，而当有一个成立之后，后面的就不执行了。 | `SELECT CASE  　WHEN 1 > 0 　THEN '1 > 0' 　WHEN 2 > 0 　THEN '2 > 0' 　ELSE '3 > 0' 　END ->1 > 0` |
| CAST(x AS type)                                              | 转换数据类型                                                 | 字符串日期转换为日期：`SELECT CAST("2017-08-29" AS DATE); -> 2017-08-29` |
| COALESCE(expr1, expr2, ...., expr_n)                         | 返回参数中的第一个非空表达式（从左向右）                     | `SELECT COALESCE(NULL, NULL, NULL, 'runoob.com', NULL, 'google.com'); -> runoob.com` |
| CONNECTION_ID()                                              | 返回服务器的连接数                                           | `SELECT CONNECTION_ID(); -> 4292835`                         |
| CONV(x,f1,f2)                                                | 返回 f1 进制数变成 f2 进制数                                 | `SELECT CONV(15, 10, 2); -> 1111`                            |
| CONVERT(s USING cs)                                          | 函数将字符串 s 的字符集变成 cs                               | `SELECT CHARSET('ABC') ->utf-8     SELECT CHARSET(CONVERT('ABC' USING gbk)) ->gbk` |
| CURRENT_USER()                                               | 返回当前用户                                                 | `SELECT CURRENT_USER(); -> guest@%`                          |
| DATABASE()                                                   | 返回当前数据库名                                             | `SELECT DATABASE();    -> runoob`                            |
| IF(expr,v1,v2)                                               | 如果表达式 expr 成立，返回结果 v1；否则，返回结果 v2。       | `SELECT IF(1 > 0,'正确','错误')     ->正确`                  |
| [IFNULL(v1,v2)](https://www.runoob.com/mysql/mysql-func-ifnull.html) | 如果 v1 的值不为 NULL，则返回 v1，否则返回 v2。              | `SELECT IFNULL(null,'Hello Word') ->Hello Word`              |
| ISNULL(expression)                                           | 判断表达式是否为 NULL                                        | `SELECT ISNULL(NULL); ->1`                                   |
| LAST_INSERT_ID()                                             | 返回最近生成的 AUTO_INCREMENT 值                             | `SELECT LAST_INSERT_ID(); ->6`                               |
| NULLIF(expr1, expr2)                                         | 比较两个字符串，如果字符串 expr1 与 expr2 相等 返回 NULL，否则返回 expr1 | `SELECT NULLIF(25, 25); ->`                                  |
| SESSION_USER()                                               | 返回当前用户                                                 | `SELECT SESSION_USER(); -> guest@%`                          |
| SYSTEM_USER()                                                | 返回当前用户                                                 | `SELECT SYSTEM_USER(); -> guest@%`                           |
| USER()                                                       | 返回当前用户                                                 | `SELECT USER(); -> guest@%`                                  |
| VERSION()                                                    | 返回数据库的版本号                                           | `SELECT VERSION() -> 5.6.34`                                 |



## 运算符

- 算术运算符
- 比较运算符
- 逻辑运算符
- 位运算符

### 算术运算符

MySQL 支持的算术运算符包括:

| 运算符   | 作用 |
| :------- | :--- |
| +        | 加法 |
| -        | 减法 |
| *        | 乘法 |
| / 或 DIV | 除法 |
| % 或 MOD | 取余 |

在除法运算和模运算中，如果除数为0，将是非法除数，返回结果为NULL。

1、加

```mysql
mysql> select 1+2;
+-----+
| 1+2 |
+-----+
|   3 |
+-----+
```

2、减

```mysql
mysql> select 1-2;
+-----+
| 1-2 |
+-----+
|  -1 |
+-----+
```

3、乘

```mysql
mysql> select 2*3;
+-----+
| 2*3 |
+-----+
|   6 |
+-----+
```

4、除

```mysql
mysql> select 2/3;
+--------+
| 2/3    |
+--------+
| 0.6667 |
+--------+
```

5、商

```mysql
mysql> select 10 DIV 4;
+----------+
| 10 DIV 4 |
+----------+
|        2 |
+----------+
```

6、取余

```mysql
mysql> select 10 MOD 4;
+----------+
| 10 MOD 4 |
+----------+
|        2 |
+----------+
```

------

### 比较运算符

SELECT 语句中的条件语句经常要使用比较运算符。通过这些比较运算符，可以判断表中的哪些记录是符合条件的。比较结果为真，则返回 1，为假则返回 0，比较结果不确定则返回 NULL。

| 符号            | 描述                       | 备注                                                         |
| :-------------- | :------------------------- | :----------------------------------------------------------- |
| =               | 等于                       |                                                              |
| <>, !=          | 不等于                     |                                                              |
| >               | 大于                       |                                                              |
| <               | 小于                       |                                                              |
| <=              | 小于等于                   |                                                              |
| >=              | 大于等于                   |                                                              |
| BETWEEN         | 在两值之间                 | >=min&&<=max                                                 |
| NOT BETWEEN     | 不在两值之间               |                                                              |
| IN              | 在集合中                   |                                                              |
| NOT IN          | 不在集合中                 |                                                              |
| <=>             | 严格比较两个NULL值是否相等 | 两个操作码均为NULL时，其所得值为1；而当一个操作码为NULL时，其所得值为0 |
| LIKE            | 模糊匹配                   |                                                              |
| REGEXP 或 RLIKE | 正则式匹配                 |                                                              |
| IS NULL         | 为空                       |                                                              |
| IS NOT NULL     | 不为空                     |                                                              |

1、等于

```mysql
mysql> select 2=3;
+-----+
| 2=3 |
+-----+
|   0 |
+-----+


mysql> select NULL = NULL;
+-------------+
| NULL = NULL |
+-------------+
|        NULL |
+-------------+
```

2、不等于

```mysql
mysql> select 2<>3;
+------+
| 2<>3 |
+------+
|    1 |
+------+
```

3、安全等于

与 **=** 的区别在于当两个操作码均为 NULL 时，其所得值为 1 而不为 NULL，而当一个操作码为 NULL 时，其所得值为 0而不为 NULL。

```mysql
mysql> select 2<=>3;
+-------+
| 2<=>3 |
+-------+
|     0 |
+-------+


mysql> select null=null;
+-----------+
| null=null |
+-----------+
|      NULL |
+-----------+

        
mysql> select null<=>null;
+-------------+
| null<=>null |
+-------------+
|           1 |
+-------------+
```

4、小于

```mysql
mysql> select 2<3;
+-----+
| 2<3 |
+-----+
|   1 |
+-----+
```

5、小于等于

```mysql
mysql> select 2<=3;
+------+
| 2<=3 |
+------+
|    1 |
+------+
```

6、大于

```mysql
mysql> select 2>3;
+-----+
| 2>3 |
+-----+
|   0 |
+-----+
```

7、大于等于

```mysql
mysql> select 2>=3;
+------+
| 2>=3 |
+------+
|    0 |
+------+
```

8、BETWEEN

```mysql
mysql> select 5 between 1 and 10;
+--------------------+
| 5 between 1 and 10 |
+--------------------+
|                  1 |
+--------------------+
```

9、IN

```mysql
mysql> select 5 in (1,2,3,4,5);
+------------------+
| 5 in (1,2,3,4,5) |
+------------------+
|                1 |
+------------------+
```

10、NOT IN

```mysql
mysql> select 5 not in (1,2,3,4,5);
+----------------------+
| 5 not in (1,2,3,4,5) |
+----------------------+
|                    0 |
+----------------------+
```

11、IS NULL

```mysql
mysql> select null is NULL;
+--------------+
| null is NULL |
+--------------+
|            1 |
+--------------+

mysql> select 'a' is NULL;
+-------------+
| 'a' is NULL |
+-------------+
|           0 |
+-------------+
```

12、IS NOT NULL

```mysql
mysql> select null IS NOT NULL;
+------------------+
| null IS NOT NULL |
+------------------+
|                0 |
+------------------+

        
mysql> select 'a' IS NOT NULL;
+-----------------+
| 'a' IS NOT NULL |
+-----------------+
|               1 |
+-----------------+
```

13、LIKE

```mysql
mysql> select '12345' like '12%';
+--------------------+
| '12345' like '12%' |
+--------------------+
|                  1 |
+--------------------+

mysql> select '12345' like '12_';
+--------------------+
| '12345' like '12_' |
+--------------------+
|                  0 |
+--------------------+
```

14、REGEXP

```mysql
mysql> select 'beijing' REGEXP 'jing';
+-------------------------+
| 'beijing' REGEXP 'jing' |
+-------------------------+
|                       1 |
+-------------------------+

mysql> select 'beijing' REGEXP 'xi';
+-----------------------+
| 'beijing' REGEXP 'xi' |
+-----------------------+
|                     0 |
+-----------------------+
```

------

### 逻辑运算符

逻辑运算符用来判断表达式的真假。如果表达式是真，结果返回 1。如果表达式是假，结果返回 0。

| 运算符号 | 作用     |
| :------- | :------- |
| NOT 或 ! | 逻辑非   |
| AND      | 逻辑与   |
| OR       | 逻辑或   |
| XOR      | 逻辑异或 |

1、与

```mysql
mysql> select 2 and 0;
+---------+
| 2 and 0 |
+---------+
|       0 |
+---------+

        
mysql> select 2 and 1;   
+---------+     
| 2 and 1 |      
+---------+      
|       1 |      
+---------+
```

2、或

```mysql
mysql> select 2 or 0;
+--------+
| 2 or 0 |
+--------+
|      1 |
+--------+

mysql> select 2 or 1;
+--------+
| 2 or 1 |
+--------+
|      1 |
+--------+

mysql> select 0 or 0;
+--------+
| 0 or 0 |
+--------+
|      0 |
+--------+

mysql> select 1 || 0;
+--------+
| 1 || 0 |
+--------+
|      1 |
+--------+
```

3、非

```mysql
mysql> select not 1;
+-------+
| not 1 |
+-------+
|     0 |
+-------+

mysql> select !0;
+----+
| !0 |
+----+
|  1 |
+----+
```

4、异或

```mysql
mysql> select 1 xor 1;
+---------+
| 1 xor 1 |
+---------+
|       0 |
+---------+

mysql> select 0 xor 0;
+---------+
| 0 xor 0 |
+---------+
|       0 |
+---------+

mysql> select 1 xor 0;
+---------+
| 1 xor 0 |
+---------+
|       1 |
+---------+

mysql> select null or 1;
+-----------+
| null or 1 |
+-----------+
|         1 |
+-----------+

mysql> select 1 ^ 0;
+-------+
| 1 ^ 0 |
+-------+
|     1 |
+-------+
```

------

### 位运算符

位运算符是在二进制数上进行计算的运算符。位运算会先将操作数变成二进制数，进行位运算。然后再将计算结果从二进制数变回十进制数。

| 运算符号 | 作用     |
| :------- | :------- |
| &        | 按位与   |
| \|       | 按位或   |
| ^        | 按位异或 |
| !        | 取反     |
| <<       | 左移     |
| >>       | 右移     |

1、按位与

```mysql
mysql> select 3&5;
+-----+
| 3&5 |
+-----+
|   1 |
+-----+
```

2、按位或

```mysql
mysql> select 3|5;
+-----+
| 3|5 |
+-----+
|   7 |
+-----+
```

3、按位异或

```mysql
mysql> select 3^5;
+-----+
| 3^5 |
+-----+
|   6 |
+-----+
```

4、按位取反

```mysql
mysql> select ~18446744073709551612;
+-----------------------+
| ~18446744073709551612 |
+-----------------------+
|                     3 |
+-----------------------+
```

5、按位右移

```mysql
mysql> select 3>>1;
+------+
| 3>>1 |
+------+
|    1 |
+------+
```

6、按位左移

```mysql
mysql> select 3<<1;
+------+
| 3<<1 |
+------+
|    6 |
+------+
```