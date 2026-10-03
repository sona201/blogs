---
title: "mysqldump 常用操作"
description: "从个人笔记仓库整理迁移"
date: "2026-10-03T22:00:00+08:00"
lastmod: "2026-10-03T22:00:00+08:00"
categories: ["mysql"]
tags: ["mysql", "mysqldump", "backup"]
draft: false
---

#mysql #sql #mysqldump
## mysqldump

### mysqldump 5.7

#### 1. mysql 导出表结构和表数据 mysqldump用法，命令行下具体用法如下:

```
mysqldump -u用戶名 -p密码 -d 数据库名 表名 > 脚本名;
```

#### 2. 导出整个数据库结构和数据
```
mysqldump -h localhost -uroot -p123456 database > dump.sql
```

#### 3. 导出单个数据表结构和数据
```
mysqldump -h localhost -uroot -p123456  database table > dump.sql
```

#### 4.导出整个数据库结构（不包含数据）
```
mysqldump -h localhost -uroot -p123456  -d database > dump.sql
```

#### 5.导出单个数据表结构（不包含数据）
```
mysqldump -h localhost -uroot -p123456  -d database table > dump.sql
```

#### 6.mysqldump 备份导出数据排除某张表

就用 `--ignore-table=dbname.tablename` 参数就行了。

```
mysqldump -uusername -ppassword -h192.168.0.1 -P3306 dbname --ignore-table=dbname.dbtanles > dump.sql
```

### mysqldump 8.0

mysqldump8.0 需要设置`--column-statistics=0`参数

```
  --column-statistics Add an ANALYZE TABLE statement to regenerate any existing
                      column statistics.
                      (Defaults to on; use --skip-column-statistics to disable.)
```

```
mysqldump --column-statistics=0 -uusername -ppassword -hhostname -P3306 cmdb_appops --ignore-table=cmdb_appops.appops_application \
--ignore-table=cmdb_appops.techops_aliyun_daily_billing_item_bill \
--ignore-table=cmdb_appops.techops_split_bill \
--ignore-table=cmdb_appops.techops_split_bill_instance \
--ignore-table=cmdb_appops.wfh_comment > dump.sql
```

建议使用参数
```
/usr/bin/mysqldump -u moba -pmoba2016 -h 10.162.84.200 --events --set-gtid-purged=OFF --single-transaction --databases db_log_zone_96005 > /data/db_backup_mysqldump/db_backup_db_log/db_log_zone_96005.sql
```
`--databases` 使用此参数导出数据时，会生成对应的创建数据库sql，免去要手动创建数据的麻烦

生成的文件样例如下

```sql
-- MySQL dump 10.13  Distrib 5.7.24-26, for Linux (x86_64)
--
-- Host: 10.162.84.200    Database: db_log_zone_96005
-- ------------------------------------------------------
-- Server version       5.7.23-log

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;
/*!50717 SELECT COUNT(*) INTO @rocksdb_has_p_s_session_variables FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_SCHEMA = 'performance_schema' AND TABLE_NAME = 'session_variables' */;
/*!50717 SET @rocksdb_get_is_supported = IF (@rocksdb_has_p_s_session_variables, 'SELECT COUNT(*) INTO @rocksdb_is_supported FROM performance_schema.session_variables WHERE VARIABLE_NAME=\'rocksdb_bulk_load\'', 'SELECT 0') */;
/*!50717 PREPARE s FROM @rocksdb_get_is_supported */;
/*!50717 EXECUTE s */;
/*!50717 DEALLOCATE PREPARE s */;
/*!50717 SET @rocksdb_enable_bulk_load = IF (@rocksdb_is_supported, 'SET SESSION rocksdb_bulk_load = 1', 'SET @rocksdb_dummy_bulk_load = 0') */;
/*!50717 PREPARE s FROM @rocksdb_enable_bulk_load */;
/*!50717 EXECUTE s */;
/*!50717 DEALLOCATE PREPARE s */;

--
-- Current Database: `db_log_zone_96005`
--

CREATE DATABASE /*!32312 IF NOT EXISTS*/ `db_log_zone_96005` /*!40100 DEFAULT CHARACTER SET latin1 */;

USE `db_log_zone_96005`;

--
-- Table structure for table `161activity_plugin_check`
--

DROP TABLE IF EXISTS `161activity_plugin_check`;
```


> CREATE DATABASE /*!32312 IF NOT EXISTS*/ `db_log_zone_96005` /*!40100 DEFAULT CHARACTER SET latin1 */;
> 这句 sql ai的解释如下

- `CREATE DATABASE`: 这是一个SQL命令，用于创建一个新的数据库。
- `/*!32312 IF NOT EXISTS*/`: 这部分是一个条件注释。MySQL会解析并执行这些注释中的内容，只要MySQL的版本高于或等于注释中指定的版本号（在这里是3.23.12）。这是一种确保向后兼容性的方法，允许在旧版本的MySQL中运行SQL脚本，而不会因为不支持的语法而出错。在这个例子中，`IF NOT EXISTS`子句会被MySQL 3.23.12或更高版本执行，它确保只有在数据库`db_log_zone_96005`不存在时才会创建它。 
- `db_log_zone_96005`: 这是要创建的数据库的名称。
- `/*!40100 DEFAULT CHARACTER SET latin1 */`: 这又是一个条件注释，它指定了数据库的默认字符集为`latin1`。这个注释中的内容会被MySQL 4.1.0或更高版本执行。字符集`latin1`是西欧语言的字符集，也称为ISO 8859-1。

综上所述，这条SQL语句的作用是：在MySQL版本3.23.12或更高的环境中，如果数据库`db_log_zone_96005`不存在，则创建它，并在MySQL版本4.1.0或更高的环境中将其默认字符集设置为`latin1`。如果数据库已经存在，这条命令将不会执行任何操作。