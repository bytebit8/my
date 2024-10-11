## 服务相关

### 安装与启动

- **MacOS With brew**

```bash
brew services install mysql
brew services list
brew services start mysql
```

- **Linux** - **ubuntu**

```bash
sudo apt install mysql-server
sudo systemctl start mysql
```

### 登录数据库

```bash
mysql -u root -p # -u 用户名 -p 密码（没有密码可以直接回车）
```

## 用户相关

### 当前用户

```sql
SELECT CURRENT_USER();
```

## 数据库相关

### 所有数据库

```sql
SHOW DATABASES;
```

### 当前数据库

```sql
SELECT DATABASE();
```

### 创建数据库

```SQL
CREATE DATABASE <database_name>;
```

### 删除数据库

```SQL
DROP DATABASE <database_name>;
```

### 切换数据库

```sql
USE <database_name>;
```

## 表相关

### 所有表

### 创建表

```SQL
CREATE TABLE <table_name>(
	field_name dataType constraints(约束条件)
)
```

**常见数据类型：**
- 数值类型
	- `INT` 4字节
	- `BIGINT` 8字节
	- `FLOAT` 单精度浮点型
	- `DOUBLE` 双精度浮点型
- 字符串类型
	- `CHAR(n)` 定长字符串，`n` 最大为 255
- 文本类型
	- `TINYTEXT` 组多 255 字节
	- `TEXT` 最多存储 65,535 字节。 
- 日期类型
	- `DATETIME` 日期时间
	- `TIMESTAMPE` 时间戳

**常见约束条件：**
* 主键：`PRIMARY KEY`
* 非空：`NOT NULL`
* 唯一：`UNIQUE`
* 默认值：`DEFAULT`

### 修改表

#### 重命名表

```sql
ALTER TABLE <table_name> RENAME TO <new_table_name>
```

#### 添加主键

```sql
ALTER TABLE <table_name> ADD PRIMARY KEY <(column_name)>
```

#### 删除主键

```sql
ALTER TABLE <table_name> DROP PRIMARY KEY
```

### 删除表

```sql
DROP TABLE <table_name>
```

## 列相关

### 所有列

```sql
DESC <table_name>;
DESCRIBE <table_name>;
```

### 追加列

```sql
ALTER TABLE <table_name> ADD <column_name> [datatype] [constraints]
```

如果要一次性追加多个列，需要重复执行 `ADD` 子句

```sql
ALTER TABLE my_table 
ADD age INT NOT NULL,
ADD price DOUBLE DEFAULT 0.0
```

### 删除列

```sql
ALTER TABLE <table_name> DROP COLUMN <column_name>
```

如果要删除多个列，需要重复执行多次 `DROP COLUMN` 子句。

```sql
ALTER TABLE my_table 
DROP COLUMN column1,
DROP COLUMN column2;
```

### 修改列类型

```sql
ALTER TABLE <table_name>
MODIFY COLUMN <column_name> <datatype> [constraints]
```

### 重命名列

```sql
ALTER TABLE <table_name> CHANGE <column_name> <new_column_name> <datatype> [constraints]
```

## 记录相关

### 插入数据

```SQL
INSERT INTO <table_name> (field1, field2, ...)
VALUES (field1_value, field2_value, ...), (field1_value, field2_value, ...), ...;
```

### 更新数据

```SQL
UPDATE <table_name>
SET field = value
WHERE condition;
```

## 安全脚本

MySQL 提供了一个安全安装脚本。可以帮助你设置root密码，删除匿名用户，禁止远程root登录以及删除测试数据库。

```bash
sudo mysql_secure_installation;
```

## FAQ

### ERROR 1698 (28000): Access denied for user 'root'@'localhost’

在新安装 MySQL 时由于 root 账户的密码是随机的并不是空密码，所以登录可能会报如上错误。解决办法就是先用系统创建的用户登录，然后再修改 root 账户密码：

下面以 Ubuntun 系统为例：

```shell
# 查看随机密码
sudo cat /etc/mysql/debian.conf;

# 使用系统创建账户与密码登录
mysql -u debian-sys-maint -p
# > enter password;

# 切换到 mysql 数据库
mysql> USE mysql;
mysql> SELECT user, plugin FROM user;
mysql> update user set plugin='mysql_native_password' where user='root';
mysql> flush privileges;

# 修改 root 用户密码
mysql> flush privileges;
mysql> alter user 'root'@'localhost' indentified by 'xxxx.';

# 重启服务
sudo systemctl stop mysql;
sudo systemctl start mysql;
```