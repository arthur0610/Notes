#
## 页

页的单位是可控的 默认为16kb 

一次磁盘IO可以获取16kb

## 页的构成
分为 页头， 页目录， 用户数据区域

### 页头
页头有两个指针分别指向前一页和后一页

### 用户数据区域 
储存数据 会根据主键排序
会影响insert的效率 但是会提升查找的效率


链表的形式存储
在insert过程中 并不仅仅会插入每一行的数据的值 而且会存储一个指针 指向下一个主键的索引

```sql
select * from t1 where a = 3
```
首先会从数据库里面取出第一页的数据 一条一条的比较主键的大小

如果链表中主键大小超过了 查询值 时 放弃整页信息

### 页目录
当一页当中 链表数据过长时 查询的效率就会受到影响
此时引出 页目录
通过把用户数据区域的每条信息 分组
页目录上储存并组织 每组主键的值 
页目录除了存储每组的值之外 还要 存一个指针去指向每组具体的位置



## Port 端口
端口号为 `3306`

## DB DBMS SQL
DB: Database
DBMS: Database Management System
Sql: Structure Query Language

## 表 table
table是数据库的基本组成单元 所有的数据都以表格的形式被组织

每一个字段应该包括哪些属性？
字段名 数据类型 相关约束

## SQL的分类
SQL语句被分为以下几类：

1. DQL (Data Query Language) 数据查询语言：`select` 查询语句
2. DDL (Data Definition Language) 数据定义语言：`create`, `drop`, `alter` 对数据进行增删改  
3. DML (Data Manipulation Language) 数据操作语言：`insert`, `update`, `delete` 对表结构的增删改
4. TCL (Transacation Control Language) 事务控制语言：`commit`, `rollback`
5. DCL (Data Control Language) 数据控制语言：`grant`, `deny`, `revoke`

## sql脚本
```sql
$source localpath.sql
```
文件扩展名为.sql 并且文件中编写了大量的sql语句 

## 

