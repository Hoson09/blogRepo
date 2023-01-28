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

### 7. AS 为列设置别名

- SELECT COUNT(\*) AS total FROM table WHERE status = 0
  统计表里面 status 为 0 的数据的总数量。(此时数据库会出现一个名字叫 total 的列,然后显示数据结果)
