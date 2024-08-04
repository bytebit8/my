## 服务相关

### 启动服务

- **MacOS With brew**

```bash
brew services list
brew services start mysql
```

- **Linux** - **ubuntu**

```bash
sudo apt install mysql-server
sudo systemctl start mysql
```

### 登录服务

```bash
mysql -u root -p # -u 用户名 -p 密码（没有密码可以直接回车）
```

## 用户相关

### 当前用户

```sql
SHOW CURRENT_USER();
```

### 安全脚本

MySQL 提供了一个安全安装脚本。可以帮助你设置root密码，删除匿名用户，禁止远程root登录以及删除测试数据库。

```bash
sudo mysql_secure_installation;
```

## 数据库相关

### 查看数据库

```sql
SHOW DATABASES
```

### 创建数据库

### 修改数据库

### 删除数据库

### 切换数据库

```sql
USE [database_name]
```

## 表相关

### 查看表

### 创建表

### 更新表

### 删除表

## FAQ

### ERROR 1698 (28000): Access denied for user 'root'@'localhost’

在新安装 MySQL 时由于 root 账户的密码是随机的并不是空密码，所以登录可能会报如上错误。解决办法就是先用系统创建的用户登录，然后再修改 root 账户密码：

```bash
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