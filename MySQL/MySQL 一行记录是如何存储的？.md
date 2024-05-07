---
title: MySQL 一行记录是如何存储的？
id: 6a3a2da1-6b5d-430e-b9b2-8cfd447b12bc
date: 2024-04-19 22:39:49
auther: herbert
cover: https://image.kangyaocoding.top/blog/post/2024-04-19T22:43:25-zvufkzlt.jpg
excerpt: MySQL 数据库的目录位置 首先，我们在 MySQL 控制台中执行SHOW VARIABLES LIKE 'datadir';就可以看到数据库文件的存放目录。 在 Linux 的默认路径中： Variable_name Value datadir /var/lib/mysql 在 Window 的
permalink: /archives/mysql-yi-xing-ji-lu-shi-ru-he-cun-chu-de
categories:
 - shu-ju-ku
tags: 
 - mysql
---

## MySQL 数据库的目录位置

首先，我们在 MySQL 控制台中执行`SHOW VARIABLES LIKE 'datadir';`就可以看到数据库文件的存放目录。

在 Linux 的默认路径中：

| Variable_name |      Value       |
| :-----------: | :--------------: |
|   datadir   | /var/lib/mysql |

在 Window 的默认路径中：

| Variable_name |                     Value                      |
| :-----------: | :--------------------------------------------: |
|   datadir   | C:\Program Flies\MySQL\MySQL Server 8.0\Data |

在 MySQL 的 InnoDB 存储引擎中，创建一个新的数据库（database）并在其中创建一张表（比如名为`test`的表），那么我们可能在 `/var/lib/mysql/database` 中看到以下三个文件：
1. `db.opt` 文件：用于存储当前数据库的默认字符集（charset）和字符校验规则（collation）。
2. `test.frm` 文件：存储了表的元数据（metadata）信息，即表的定义。它包含了表的列信息（列名、数据类型、约束等）、索引信息以及表的其他属性。
3. `test.ibd`文件：存储了表的数据。表的数据可以存储在共享表空间文件（通常是 ibdata1 ）里，也可以存储在独占表空间文件（以`.ibd`为扩展名）中。将`innodb_file_per_table`参数设置为 1，则每张表的数据、索引等信息将被存储在一个独占的表空间文件中，即`.ibd`文件。（从MySQL 5.6.6版本开始， innodb_file_per_table 的默认值就是 1）

## InnoDB 行格式有哪些？
InnoDB 存储引擎在 MySQL 中支持多种行格式，以下是 InnoDB 支持的主要行格式：

1. **REDUNDANT**：
    - MySQL 5.0 之前版本的默认行格式。
    - REDUNDANT 行格式已经较少使用（不推荐）。
2. **COMPACT**：
    - MySQL 5.0 及之后版本的默认行格式。
    - 对于可变长度的列（如 VARCHAR、TEXT），前 768 字节存储在记录中，超出的部分存储在溢出页中。
    - 除了存储字段值外，还会记录变长字段长度列表、NULL 值列表以及记录头信息。
    - 适合处理包含大量可变长度列的数据。
![2024-04-19T22:43:25-esycaxrk.jpg](https://image.kangyaocoding.top/blog/post/2024-04-19T22:43:25-esycaxrk.jpg)

3. **DYNAMIC**：
    - 与 COMPACT 类似，但在处理可变长度字段时有所不同。
    - 对于可变长度的字段，只有前 20 字节的指针存储在记录中，实际的数据存储在溢出页中。
4. **COMPRESSED**：
    - 这种格式会对记录进行压缩存储，以节省磁盘空间。
    - 压缩和解压缩操作会增加 CPU 的开销。
![2024-04-19T22:43:25-zvufkzlt.jpg](https://image.kangyaocoding.top/blog/post/2024-04-19T22:43:25-zvufkzlt.jpg)


在创建或修改表时，可以使用 `ROW_FORMAT` 选项来指定行格式。如：

```sql
CREATE TABLE my_table (...) ROW_FORMAT=COMPACT;
```

或者修改现有表的行格式：

```sql
ALTER TABLE my_table ROW_FORMAT=DYNAMIC;
```

## COMPACT 行格式


![2024-04-19T22:43:25-ktnteete.jpg](https://image.kangyaocoding.top/blog/post/2024-04-19T22:43:25-ktnteete.jpg)


一条完整的记录分为**记录的额外信息**和**记录的真实数据**两个部分。<br>
### 1. 记录的额外信息

这部分包含了三个主要组件。

- **变长字段长度列表**：存储了`varchar`类型字段的长度。因为 varchar 字段的长度是可变的，所以需要列表来记录每个 varchar 字段实际占用的空间大小。
    
- **NULL值列表**：用二进制位（0或1）来标识记录中的每个字段是否为NULL。1表示该字段为空（NULL），0表示该字段不为空。
    
- **记录头信息**：包括`delete_mask`（标识记录是否被逻辑删除），`next_record`（指向下一条记录的指针，用于链表结构的维护），`record_type`（标识记录的类型，如普通记录、B+树非叶子节点记录等）。
    

### 2. 记录真实数据

在记录的额外信息之后，紧接着是记录的真实数据部分。

- **row_id**：这是一个隐藏列，用于唯一标识InnoDB表中的每条记录。如果表有主键，那么主键的值就是row_id；如果没有主键，InnoDB会生成一个唯一的row_id。
    
- **trx_id**：记录相关的事务ID。它用于标识创建或最后修改此记录的事务。这是实现多版本并发控制（MVCC）的关键，因为MVCC允许不同的事务看到数据的不同版本。
    
- **roll_pointer**：指向记录的前一个版本。当记录被修改时，InnoDB不会直接覆盖原始数据，而是保留原始版本，并通过roll_pointer指向它。所以，系统可以访问记录的所有历史版本，从而支持高并发性能。