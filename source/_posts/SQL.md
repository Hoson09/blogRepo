---
title: SQL
date: 2023-01-24 11:09:15
tags: sql
---

# 1. 数据库以及分类

- MySQL 数据库，Oracle 数据库，SQL Server 数据库，属于关系型数据库（SQL 数据库）。
- MongoDB 数据库，属于非关系型数据库（非 SQL 数据库）

# 2. 传统数据库

## 1.传统数据库的数据组织结构

- 数据库（database）
  - 数据表（table）
    - 数据行（row）
      - 字段（field）

## 2.安装并配置 MySQL 数据

- MySQL server:`专门用来提供数据存储和服务的软件`
- MySQL WorkBench：`可视化的MySQL的管理工具`

## 3.SQL 言语

### 1.SELECT 语句

- SELECT \* FROM 表名称
- SELECT 列名称 FROM 表名称

### 2. INSERT INTO 语句

- INSERT INTO table_name(row1,row2) VALUES (val1,val2)

### 3. UPDATE 语句

- UPDATE table SET filed = 'val' WHERE filed = 'something'

### 4. DELETE 语句

- DELETE FROM tablen WHERE field = 'val'

### 5. WHERE 子句 （条件限定语句）

- SQL 语句 + WHERE 列 运算符 值

### 6. AND 和 OR 运算符

- 一般和 WHERE 联合起来使用,提供多种条件查询

### 7. ORDER BY 语句

#### 1.排序

- SELECT \* FROM tbale ORDER BY status;（默认升序）
- SELECT \* FROM table ORDER BY status ASC;（升序）
- SELECT \* FROM table ORDER BY id DESC;(降序排序)

#### 2.多重排序

- SELECT \* FROM table ORDER BY status DESC,userName ASC
  先根据 status 进行降序排序，再根据 userName 进行升序排序。

### 6. COUNT(\*)函数

- SELECT COUNT(\*) FROM table WHERE status = 0
  统计表里面 status 为 0 的数据的总数量。(此时数据库会出现一个名字叫 COUNT(\*)的列,然后显示数据的多少个)
- SELECT COUNT(column_name) FROM table_name;
  COUNT(column_name) 函数返回指定列的值的数目（NULL 不计入）
- SELECT COUNT(DISTINCT column_name) FROM table_name;
  COUNT(DISTINCT column_name) 函数返回指定列的不同值的数目：

### 7. AS 为列设置别名

- SELECT COUNT(\*) AS total FROM table WHERE status = 0
  统计表里面 status 为 0 的数据的总数量。(此时数据库会出现一个名字叫 total 的列,然后显示数据结果)

## 4.SQL 高级

### 1. like 操作符 （与 where 结合起来使用，进行模糊查询）

```sql
SELECT column1, column2, ... FROM table_name WHERE column LIKE pattern;
-- password 以11开头
SELECT * FROM users WHERE password LIKE '11%'
-- userName以s结尾
SELECT * FROM usebrs WHERE userName LIKE '%s'
-- userName 包含b的
SELECT * FROM users WHERE userName LIKE '%b%'
-- userName 不包含b的
SELECT * FROM users WHERE userName Not LIKE '%b%'
```

### 2. 通配符

```sql
-- % 代替0个或者多个字符
SELECT * FROM users WHERE userName Not LIKE '%b%'
-- _ 代替一个字符
SELECT * FROM users WHERE userName LIKE '_s';
-- [charlist] 字符类中的任何单一字符 [^charlist] 不在字符列中的任何单一字符
-- userName  不以 "G"、"F" 或 "x" 开始的数据
SELECT * FROM users WHERE userName Not REGEXP '^[GFx]';
-- userName  以 a 到 c 字母开头的网站
SELECT * FROM users WHERE userName REGEXP '^[a-c]';
-- userName  不以 a 到 c 字母开头的网站
SELECT * FROM users WHERE userName REGEXP '^[^a-c]';
```

### 3.IN 操作符

```sql
SELECT column1, column2, ... FROM table_name WHERE column IN (value1, value2, ...);
-- 选取 userName 为zs，bbb或者是ls的数据 并以id降序
SELECT * FROM users WHERE userName IN ('zs','bbb','ls') order by id desc
```

### 4. BETWEEN 操作符

- BETWEEN 操作符选取介于两个值之间的数据范围内的值。这些值可以是数值、文本或者日期。

```sql
-- 语句选取 alexa 介于 1 和 20 之间的所有网站
SELECT * FROM Websites WHERE alexa BETWEEN 1 AND 20;
SELECT * FROM Websites WHERE (alexa BETWEEN 1 AND 20) AND country NOT IN ('USA', 'IND')
```

### 5. JOIN

- LEFT JOIN
- RIGHT JOIN
- INNER JOIN
- LEFT JOIN
- OUTER JOIN
