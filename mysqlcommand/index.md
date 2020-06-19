# Mysql 常用命令


## 登录

```sql
mysql -u root -p密码(不推荐)

mysql -u root -p回车后输入密码

```

## 数据库相关

### 创建数据库

```sql
create database 数据库名称;
```

### 显示现有数据库

```sql
show databases;
```

### 显示建库语句

```sql
show create database 数据库名称;
```

### 使用某个数据库

```sql
use 数据库名称;
```

### 删除某个数据库

```sql
drop database 数据库名称;
```

## 常用操作

### 显示建表语句

```sql
show create table 表名;
```

### 添加字段

```sql
ALTER TABLE 表名 ADD 字段名1 类型名(长度) NOT NULL DEFAULT '' COMMENT '备注1',
                ADD 字段名2 类型名(长度) NOT NULL DEFAULT '' COMMENT '备注2';
```

### 修改字段

```sql
alter table 表名
modify 字段名1 类型名(长度) NOT NULL DEFAULT '' COMMENT '备注1',
modify 字段名2 类型名(长度) NOT NULL DEFAULT '' COMMENT '备注2';
```

### 移动字段顺序

```sql
1.跟在某字段后面
alter table 表名 modify 字段名 类型名(长度) NOT NULL DEFAULT '' COMMENT '备注' after 跟着的字段(排在谁后面)
2.直接到首位
alter table 表名 modify 字段名 类型名(长度) NOT NULL DEFAULT '' COMMENT '备注' first
```

### 删除字段

```sql
alter table 表名 drop column 字段名1,drop column 字段名2;
```

### 添加索引

```sql
ALTER TABLE  `表名` ADD INDEX 索引名1 ( `字段名`), ADD INDEX 索引名2 ( `字段名1`,`字段名2`), ADD INDEX 索引名3 ( `字段名`);
```

### 删除索引

```sql

alter table 表名 drop index 索引名1,drop index 索引名2;

删除主键 谨慎操作
alter table 表名 drop primary key;
```

### 修改索引

```sql
mysql 修改索引需要先删除索引再增加索引
```

### 重置自增值

```sql
alter table 表名 auto_increment = 1;
```

