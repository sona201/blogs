---
title: "MySQL SQL 常用命令"
description: "从个人笔记仓库整理迁移"
date: "2026-10-03T22:00:00+08:00"
lastmod: "2026-10-03T22:00:00+08:00"
categories: ["mysql"]
tags: ["mysql", "sql", "command"]
draft: false
---

#sql #mysql #command

## 1 库表结构操作

### 1.1 建库

#### 1.1.1 DDL 创建数据库设置字符串集utf8

```sql
CREATE DATABASE `db_name` CHARACTER SET utf8 COLLATE utf8_general_ci;
CREATE DATABASE IF NOT EXISTS db_name;
```

|字符集|长度|说明|
|---|---|---|
|GBK|2|支持中文，但是不是国际通用字符集|
|UTF-8|3|支持中英文混合场景，是国际通用字符集|
|latin1|2|MySQL默认字符集|
|utf8mb4|4|完全兼容UTF-8，用四个字节存储更多的字符|

#### 1.1.2 DDL 删库

```sql
DROP DATABASE `db_name`;
```

用户赋权

#### 查询当前用户权限

```sql
show grants;
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'username'@'%' WITH GRANT OPTION
```

#### 删除权限
```sql
revoke SELECT, INSERT, UPDATE, DELETE ON `db_name`.`table_name` from 'user_name'@'%';
```

### mysql 8.0 创建用户
```sql
create user 'username'@'host' identified by 'password';
```

### 给用户赋权
```sql
GRANT all privileges ON zabbix.* TO zabbix@localhost identified BY 'password';

-- mysql 8.0 用户授权
grant all privileges on *.* to 'username'@'%' with grant option;
grant all privileges on jumpserver.* to 'jumpserver'@'127.0.0.1' with grant option;

-- 表粒度赋权，不能一次执行多条赋权
GRANT SELECT ON `db_zone_global`.`t_activity` TO 'activity_rd'@'%';
GRANT SELECT ON `db_zone_global`.`t_activity_battle` TO 'activity_rd'@'%';
GRANT SELECT ON `db_web_gm`.`t_common_op_log` TO 'activity_rd'@'%';
```

#### 赋权

```
GRANT USAGE ON *.* TO 'prod_order_center'@'%' GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER ON `order_center`.* TO 'prod_order_center'@'%';

select user,host from mysql.user;
show grants for 'prod_sysmgr';
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER ON `yimi`.`erp_clients` TO 'prod_sysmgr'@'%'
```

#### 授权之后刷新权限
```sql
flush privileges;
```

#### 查询用户权限

查询指定用户权限
```sql
show grants for 'stage-user'@'%';

-- 查看怎么进入的
select user();
```

#### 删除用户

```sql
drop user Jenkins@'localhost';
```

数据库新建用户
数据库用户修改密码
#### 修改用户密码(登录状态)
```
GRANT SELECT, INSERT, UPDATE, DELETE  ON *.* TO 'csz'@'%' identified by  "asdfghjkl";
set password for 'user'@'%' = password('your_password');

UPDATE mysql.user SET password=PASSWORD(’新密码’) WHERE User=’root’;
# the first time update password
SET PASSWORD = PASSWORD('password');

# mysql8
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```
数据库用户赋权

### 1.2 数据库表的增删改查

#### 1.2.1 DDL 新增表 建表

```sql
CREATE TABLE `bo_eu_yw_gzsbsq_guanyuan` (
  `id` char(36) NOT NULL COMMENT '唯一标志',
  `orgid` varchar(36) DEFAULT '' COMMENT '组织id',
  `bindid` char(36) NOT NULL DEFAULT '' COMMENT '关联值',
  `createdate` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `createuser` varchar(36) DEFAULT NULL COMMENT '创建人',
  `updatedate` timestamp NULL DEFAULT NULL COMMENT '更新时间',
  `updateuser` varchar(36) DEFAULT NULL COMMENT '更新人',
  `processdefid` char(36) DEFAULT '' COMMENT '流程定义id',
  `isend` smallint(1) NOT NULL DEFAULT '0' COMMENT '是否结束',
  `djbh` varchar(128) DEFAULT '' COMMENT '单据编号',
  `sqrq` datetime DEFAULT NULL COMMENT '申请日期',
  `sqr` varchar(128) DEFAULT '' COMMENT '上报人',
  `sqrzh` varchar(128) DEFAULT '' COMMENT '申请人账户',
  `suborderno` varchar(128) DEFAULT '' COMMENT '子工单号',
  `subbindid` varchar(128) DEFAULT '' COMMENT '子工单bindid',
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `updated_at` timestamp NULL DEFAULT NULL COMMENT '更新时间',
  `status_flag` int(11) NOT NULL DEFAULT '0' COMMENT '更新状态',
  `handler_name` varchar(255) DEFAULT '' COMMENT '处理人',
  `handler_dep` varchar(255) DEFAULT '' COMMENT '处理人部门',
  `comment_create_success_time` timestamp NULL DEFAULT CURRENT_TIMESTAMP COMMENT '制单成功时间',
  `isend_name` varchar(128) DEFAULT '' COMMENT '是否结束name',
  `sffp_name` varchar(128) DEFAULT '' COMMENT '是否复盘name',
  `sfyjkfx_name` varchar(128) DEFAULT '' COMMENT '是否有监控发现name',
  `sfqx_name` varchar(128) DEFAULT '' COMMENT '是否缺陷name',
  `circulation_times` varchar(128) DEFAULT '' COMMENT '转办次数',
  PRIMARY KEY (`bindid`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 ROW_FORMAT=DYNAMIC COMMENT='故障上报观远报表关联字段';
```


#### 1.2.2 DDL 删除表

```sql
-- 直接删除表，不检查是否存在
DROP TABLE table_name ;
```

```sql
-- 检查是否存在表，然后删除表
DROP TABLE IF EXISTS table_name;
```


#### 1.2.3 DDL 表新增字段，并指定字段位置

```sql
ALTER TABLE `cmdb_appops`.`sysops_aliyun_sls` ADD `product_key` varchar(50) NOT NULL DEFAULT 'NA' COMMENT '数字产品 key' AFTER `project_id`;
```
#### 1.2.4 DDL 修改表字段

```sql
ALTER TABLE cmdb_appops.appops_kuber_dump_file CHANGE `oss_download_url` `download_url` varchar(350) NOT NULL DEFAULT 'NA' COMMENT '下载url';
```

修改表字段属性(类型, 长度)
```sql
ALTER TABLE `cmdb_appops`.`techops_split_bill_instance` MODIFY `project_id_name` varchar(150) NOT NULL DEFAULT 'NA' COMMENT '关联项目名';
```
#### 修改表字段顺序

也可以用change

```
alter table app03_book modify `price` decimal(8,2) DEFAULT NULL  after publishDate;
```

#### mysql自增ID起始值修改方法
```
alter table app03_authordetail AUTO_INCREMENT=10000;

# 创建语句

CREATE TABLE `orders` (
  `order_num` int(11) NOT NULL auto_increment,
  `order_date` datetime NOT NULL,
  `cust_id` int(11) NOT NULL,
  PRIMARY KEY  (`order_num`),
  KEY `fk_orders_customers` (`cust_id`),
  CONSTRAINT `fk_orders_customers` FOREIGN KEY (`cust_id`) REFERENCES `customers` (`cust_id`) ON DELETE CASCADE ON UPDATE CASCADE
) ENGINE=InnoDB AUTO_INCREMENT=10000 DEFAULT CHARSET=utf8;
```

#### 查看建表语句

```
show create table [table_name]
```

#### 1.2.5 DDL 删除表字段

```sql
ALTER TABLE `cmdb_appops`.`techops_split_bill_instance` DROP `product_key`;
```

数据库表的索引操作

#### 1.2.6 DDL 新增表索引

```sql
ALTER TABLE `cmdb_appops`.`appops_kubernetes_events` ADD UNIQUE INDEX `uid_v_unique_index`(`metadata_uid`, `metadata_resource_version`);

ALTER TABLE `question_use_records` ADD INDEX `IDX_question_id`(`question_id`,`student_id`) USING BTREE;
```

#### 1.2.7 DDL 删除表索引

```sql
ALTER TABLE `cmdb_appops`.`appops_kubernetes_events` DROP INDEX `uid_v_unique_index`;
```

#### DCL 控制语句，设置自动提交
```sql
set autocommit=0;
```

#### 修改index值，新表默认从1开始
```sql
ALTER TABLE project_mark_table auto_increment = 2;
```

表数据的增删改查

#### DML 表数据插入
```sql
INSERT INTO cmdb_appops.techops_bill_flow_process_node_definition (node_id,node_name,next_node,back_node,repeat_exec,task_name,comment,created_at,updated_at) VALUES
  ('1','起始','2','1',0,'NA','不可重复，赛峰解析阿里云数据','2023-03-01 23:59:35','2023-03-06 23:35:29'),
  ('2','初始化分账账单','3','1',0,'apps.billmanagement.task.split_bill_2_instance.handle_split_bill_to_instance','不可重复，将阿里云数据生成id/split_id唯一','2023-03-06 23:33:11','2023-03-06 23:36:20'),
  ('3','一级实例id数据生成','4','2',0,'apps.billmanagement.task.split_bill_2_instance.handle_split_bill_to_instance_rule_product','不可重复，将阿里云数据生成id/split_id唯一','2023-03-01 23:59:35','2023-03-06 23:35:30'),
  ('4','一级分账规则生成','5','3',0,'apps.billmanagement.task.split_bill_2_instance.handle_split_bill_to_instance_rule_product_init','不可重复，1将阿里云数据生成产品唯一，2同时获取上个月分摊逻辑数据写入当月数据','2023-03-01 23:59:35','2023-03-06 23:35:30'),
  ('5','一级特殊实例id配置','6','4',0,'apps.billmanagement.task.split_bill_2_instance.handle_split_bill_instance_particular_init','不可重复，将上个月特殊实例id拷贝写入当月','2023-03-01 23:59:35','2023-03-06 23:35:30'),
  ('6','一级分账规则写入实例','7','5',1,'apps/billmanagement/task/split_bill_rule_to_instance.handle_split_bill_rule_to_instance_main','根据一级分摊规则写入数据','2023-03-01 23:59:35','2023-03-06 23:35:30'),
  ('7','二级分摊配置','8','6',0,'NA','拷贝同步上个月录入二级分摊规则配置','2023-03-01 23:59:35','2023-03-06 23:35:30'),
  ('8','二级分摊规则关联','9','7',1,'NA','同步上个月录入二级分摊关联','2023-03-01 23:59:35','2023-03-06 23:35:30'),
  ('9','二级分摊结果生成','10','8',1,'NA','根据关联逻辑分摊二级账单','2023-03-01 23:59:35','2023-03-06 23:35:30'),
  ('10','三级分摊配置','11','9',0,'NA','拷贝同步上个月录入三级分摊规则配置','2023-03-01 23:59:35','2023-03-06 23:35:30'),
  ('11','三级分摊规则关联','12','10',1,'NA','同步上个月录入三级分摊关联','2023-03-01 23:59:35','2023-03-06 23:35:30'),
  ('12','三级分摊结果生成','12','11',1,'NA','根据关联逻辑分摊三级账单','2023-03-01 23:59:35','2023-03-06 23:35:30');
```

#### DML 表删除数据

```sql
DELETE FROM `company_devops`.`rbac_permission` WHERE `id`=169;
```

#### DML 清空表数据

```sql
TRUNCATE TABLE table_name;
```

#### DML 表数据更新

```sql
-- 使用语法 UPDATE table_name SET field1=new-value1, field2=new-value2 [WHERE Clause]

UPDATE `company_devops`.`rbac_permission` SET status=1,permission=2 WHERE `id`=168;
```


#### DML 表数据查询
```
select * from server_domain_and_port where type="1";
```


#### DML 查询指定行数的数据，从第10行向后查询5条数据
```sql
SELECT userMobile FROM mis_user_info limit 10,5;
```


### sql 其他操作


https://blog.csdn.net/yageeart/article/details/7973381


### 手动灌sql

```
mysql -h db.adress.mysql.rds.aliyuncs.com -P 3306 -u user_name -p'password' db_name < insert.sql
```
也可以进入mysql，source file


#### 查看慢查询设置时间，单位秒
```
show VARIABLES like 'long_query_time';   
```

#### 查询存储过程创建命令
```
show create procedure sp_add_view_lesson_doc;
```

#### 查询从库master信息

1. 查看主库状态
```sql
SHOW MASTER STATUS;
```
主库状态结果
```
mysql> show master status\G
*************************** 1. row ***************************
             File: master-bin.000868
         Position: 782491827
     Binlog_Do_DB: 
 Binlog_Ignore_DB: information_schema,mysql,test,performance_schema
Executed_Gtid_Set: 7fb22524-166f-11ef-94d3-3cecefba3d48:1-9691144
1 row in set (0.01 sec)

mysql> Bye
```
2. 查看从库状态
```sql
SHOW SLAVE STATUS\G
```
查看结果
```
mysql> show slave status\G
*************************** 1. row ***************************
               Slave_IO_State: Reconnecting after a failed master event read
                  Master_Host: 10.64.57.88
                  Master_User: replicate
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: master-bin.044525
          Read_Master_Log_Pos: 948993872
               Relay_Log_File: relay-bin.133574
                Relay_Log_Pos: 948994087
        Relay_Master_Log_File: master-bin.044525
             Slave_IO_Running: Connecting
            Slave_SQL_Running: Yes
              Replicate_Do_DB: 
          Replicate_Ignore_DB: information_schema,mysql,test,performance_schema
           Replicate_Do_Table: 
       Replicate_Ignore_Table: 
      Replicate_Wild_Do_Table: 
  Replicate_Wild_Ignore_Table: 
                   Last_Errno: 0
                   Last_Error: 
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 948993872
              Relay_Log_Space: 948994345
              Until_Condition: None
               Until_Log_File: 
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File: 
           Master_SSL_CA_Path: 
              Master_SSL_Cert: 
            Master_SSL_Cipher: 
               Master_SSL_Key: 
        Seconds_Behind_Master: NULL
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 2003
                Last_IO_Error: error reconnecting to master 'replicate@10.64.57.88:3306' - retry-time: 60  retries: 11
               Last_SQL_Errno: 0
               Last_SQL_Error: 
  Replicate_Ignore_Server_Ids: 
             Master_Server_Id: 11
                  Master_UUID: d2748d8a-3475-11eb-b6ae-ac1f6bf552dc
             Master_Info_File: /data/percona_slave/master.info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
      Slave_SQL_Running_State: Slave has read all relay log; waiting for more updates
           Master_Retry_Count: 86400
                  Master_Bind: 
      Last_IO_Error_Timestamp: 240604 22:33:51
     Last_SQL_Error_Timestamp: 
               Master_SSL_Crl: 
           Master_SSL_Crlpath: 
           Retrieved_Gtid_Set: d2748d8a-3475-11eb-b6ae-ac1f6bf552dc:1-1098648649
            Executed_Gtid_Set: d2748d8a-3475-11eb-b6ae-ac1f6bf552dc:1-1098648649
                Auto_Position: 0
         Replicate_Rewrite_DB: 
                 Channel_Name: 
           Master_TLS_Version: 
1 row in set (0.00 sec)v
```

在主库查看从库的信息
```sql
show processlist;
```

```
mysql> show processlist;
+--------+-----------+---------------------+----------------------------+-------------+---------+---------------------------------------------------------------+------------------+-----------+---------------+
| Id     | User      | Host                | db                         | Command     | Time    | State                                                         | Info             | Rows_sent | Rows_examined |
+--------+-----------+---------------------+----------------------------+-------------+---------+---------------------------------------------------------------+------------------+-----------+---------------+
|  42423 | replicate | 10.117.62.200:34662 | NULL                       | Binlog Dump | 1189092 | Master has sent all binlog to slave; waiting for more updates | NULL             |         0 |             0 |
|  70508 | moba      | 10.117.67.101:35914 | db_zone_battlereport_12460 | Sleep       |      13 |                                                               | NULL             |         0 |             0 |
|  70552 | moba      | 10.117.67.101:36140 | db_zone_12460              | Sleep       |      13 |                                                               | NULL             |         0 |             1 |
|  70651 | moba      | 10.117.67.101:36302 | db_zone_12460              | Sleep       |       3 |                                                               | NULL             |         1 |             1 |
|  70680 | moba      | 10.117.67.101:36408 | db_zone_battlereport_12460 | Sleep       |       1 |                                                               | NULL             |         6 |             6 |
|  71506 | moba      | 10.117.67.101:37400 | db_zone_battlereport_8074  | Sleep       |       0 |                                                               | NULL             |         0 |             0 |
|  71544 | moba      | 10.117.67.101:37624 | db_zone_8074               | Sleep       |       9 |                                                               | NULL             |         0 |             1 |
|  71590 | moba      | 10.117.67.101:37786 | db_zone_8074               | Sleep       |       5 |                                                               | NULL             |         1 |             1 |
|  71623 | moba      | 10.117.67.101:37890 | db_zone_battlereport_8074  | Sleep       |       0 |                                                               | NULL             |         5 |             5 |
|  71941 | moba      | 10.117.67.101:38536 | db_zone_battlereport_9414  | Sleep       |       6 |                                                               | NULL             |         0 |             0 |
|  71985 | moba      | 10.117.67.101:38748 | db_zone_9414               | Sleep       |       6 |                                                               | NULL             |         0 |             1 |
|  72070 | moba      | 10.117.67.101:39002 | db_zone_9414               | Sleep       |       2 |                                                               | NULL             |         1 |             1 |
|  72100 | moba      | 10.117.67.101:39136 | db_zone_battlereport_9414  | Sleep       |       8 |                                                               | NULL             |         6 |             6 |
|  72756 | moba      | 10.117.67.101:40528 | db_zone_battlereport_12582 | Sleep       |       3 |                                                               | NULL             |         0 |             0 |
|  72801 | moba      | 10.117.67.101:40762 | db_zone_12582              | Sleep       |       3 |                                                               | NULL             |         0 |             1 |
|  72886 | moba      | 10.117.67.101:41066 | db_zone_12582              | Sleep       |       1 |                                                               | NULL             |         1 |             1 |
|  72919 | moba      | 10.117.67.101:41218 | db_zone_battlereport_12582 | Sleep       |       8 |                                                               | NULL             |         1 |             1 |
|  73191 | moba      | 10.117.67.101:41966 | db_zone_battlereport_12738 | Sleep       |       0 |                                                               | NULL             |         0 |             0 |
|  73226 | moba      | 10.117.67.101:42222 | db_zone_12738              | Sleep       |      13 |                                                               | NULL             |         0 |             1 |
|  73288 | moba      | 10.117.67.101:42510 | db_zone_12738              | Sleep       |       0 |                                                               | NULL             |         1 |             1 |
|  73322 | moba      | 10.117.67.101:42678 | db_zone_battlereport_12738 | Sleep       |      17 |                                                               | NULL             |         6 |             6 |
| 153875 | moba      | 10.117.67.101:50042 | db_zone_battlereport_2295  | Sleep       |       1 |                                                               | NULL             |         0 |             0 |
| 153913 | moba      | 10.117.67.101:50366 | db_zone_2295               | Sleep       |       1 |                                                               | NULL             |         0 |             1 |
| 153991 | moba      | 10.117.67.101:50754 | db_zone_2295               | Sleep       |       7 |                                                               | NULL             |         1 |             1 |
| 154006 | moba      | 10.117.67.101:50850 | db_zone_battlereport_2295  | Sleep       |      18 |                                                               | NULL             |         6 |             6 |
| 154644 | moba      | 10.117.67.101:54054 | db_zone_9414               | Sleep       |       0 |                                                               | NULL             |         0 |             0 |
| 154645 | moba      | 10.117.67.101:54132 | db_zone_9414               | Sleep       |      22 |                                                               | NULL             |         0 |             0 |
| 154646 | moba      | 10.117.67.101:54220 | db_zone_12460              | Sleep       |       3 |                                                               | NULL             |         0 |             0 |
| 154647 | moba      | 10.117.67.101:54262 | db_zone_12460              | Sleep       |      12 |                                                               | NULL             |         0 |             0 |
| 154648 | moba      | 10.117.67.101:54264 | db_zone_12460              | Sleep       |      13 |                                                               | NULL             |         0 |             0 |
| 154652 | moba      | 10.117.67.101:54358 | db_zone_8074               | Sleep       |       0 |                                                               | NULL             |         0 |             0 |
| 154664 | moba      | 10.117.67.101:54652 | db_zone_8074               | Sleep       |       6 |                                                               | NULL             |         0 |             0 |
| 154665 | moba      | 10.117.67.101:54654 | db_zone_8074               | Sleep       |       7 |                                                               | NULL             |         0 |             0 |
| 154666 | moba      | 10.117.67.101:54738 | db_zone_9414               | Sleep       |      21 |                                                               | NULL             |         0 |             0 |
| 154668 | moba      | 10.117.67.101:54892 | db_zone_2295               | Sleep       |       0 |                                                               | NULL             |         0 |             0 |
| 154682 | moba      | 10.117.67.101:55058 | db_zone_2295               | Sleep       |       9 |                                                               | NULL             |         0 |             0 |
| 154683 | moba      | 10.117.67.101:55060 | db_zone_2295               | Sleep       |       9 |                                                               | NULL             |         0 |             0 |
| 154726 | moba      | 10.117.67.101:55610 | db_zone_12582              | Sleep       |       2 |                                                               | NULL             |         0 |             0 |
| 154727 | moba      | 10.117.67.101:55666 | db_zone_12582              | Sleep       |      36 |                                                               | NULL             |         0 |             0 |
| 154728 | moba      | 10.117.67.101:55667 | db_zone_12582              | Sleep       |      37 |                                                               | NULL             |         0 |             0 |
| 154771 | moba      | 10.117.67.101:56176 | db_zone_12738              | Sleep       |       0 |                                                               | NULL             |         0 |             0 |
| 154786 | moba      | 10.117.67.101:56284 | db_zone_12738              | Sleep       |      18 |                                                               | NULL             |         0 |             0 |
| 154787 | moba      | 10.117.67.101:56300 | db_zone_12738              | Sleep       |     198 |                                                               | NULL             |         0 |             0 |
| 321956 | moba      | 10.117.67.101:53854 | NULL                       | Query       |       0 | starting                                                      | show processlist |         0 |             0 |
+--------+-----------+---------------------+----------------------------+-------------+---------+---------------------------------------------------------------+------------------+-----------+---------------+
44 rows in set (0.00 sec)

mysql> Bye
```


删除索引

```sql
ALTER TABLE `duty_schedule_group_rule_relationship` DROP INDEX duty_schedule_group_rule_group_id_rule_id_a918fd5a_uniq;
```

复杂sql

```sql
select
	distinct server
from
	t_server
where
	app = 'MOBA'
	and server not in (
		select
			distinct server
		from
			t_server
		where
			IF(
				SUBSTRING_INDEX(division, '.',-1)>1000,
				FLOOR(SUBSTRING_INDEX(division, '.',-1)/ 1000),
				SUBSTRING_INDEX(division, '.',-1)
			) in (55, 56, 57, 58, 59)
	)
	and
		IF(
			SUBSTRING_INDEX(division, '.',-1)>1000,
			FLOOR(SUBSTRING_INDEX(division, '.',-1)/ 1000),
			SUBSTRING_INDEX(division, '.',-1)
		) in (100, 200);
```
res
```
AccountProtectServer
DataBridgeServer
DataQueryServer
GMServer
NetEaseYidunServer
PassProxyServer
ProtoGateServer
SmsServer
TranslateServer
ZoneManagerServer
```

server 的分区是 100，200，且不是55, 56, 57, 58, 59分区