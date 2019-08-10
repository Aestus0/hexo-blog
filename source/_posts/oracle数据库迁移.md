---
title: oracle数据库迁移
date: 2019-08-10 14:05:23
tags: sql
---
![7Oji-C0H-YY.jpg](https://i.loli.net/2019/08/10/XqWPAg2JGQ9dVOD.jpg)
# oracle数据库迁移

## 步骤

1.创建表空间
```oracle-sql
create tablespace RH_DEV 
datafile 'D:\DATABASE\ORACLE\ORACLE11G\ORADATA\HLS\RH_DEV.DBF' size 1024M --存储地址 初始大小1G
autoextend on next 10M maxsize unlimited   --每次扩展10M，无限制扩展
EXTENT MANAGEMENT local  autoallocate
segment space management auto;
```
2.创建用户
```oracle-sql
create user RH_DEV  identified by rh_dev 
default tablespace RH_DEV
temporary tablespace TEMP
profile DEFAULT;
```
3.赋予用户权限
```oracle-sql
grant dba to RH_DEV;
grant connect to RH_DEV;
grant resource to RH_DEV;
```
4.impdp
```oracle-sql
mpdp scott/tiger DIRECTORY=dpdata1 DUMPFILE=expdp.dmp SCHEMAS=scott;
```