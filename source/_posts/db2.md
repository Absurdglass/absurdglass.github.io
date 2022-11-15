---
title: db2语法
date: 2022-11-10 09:52:31
tags:
---
### 新增表字段
``` bash
alter table 表名 add 字段名 数据类型 default 默认值
alter table BANK_SOKECT_INFO ADD COLUMN DEPTNO  varchar(8);
```
### 删除表字段
``` bash
ALTER table 表名 DROP column 字段名
```
### 修改字段类型
``` bash
alter table studentinfo alter column stutel set data type char(11);
```