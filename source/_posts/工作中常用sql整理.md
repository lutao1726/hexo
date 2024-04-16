---
title: 工作中常用到的sql
tags: [Linux, 运维, sql]
categories: 工作整理
date:  2018/8/16 17:29
---
<!-- more -->
工作整理-常用到的sql
===============

去重
------
```sql
    --去重并插入新表中
    select * 
    into 表1_dist
    from 表1 pa 
    where pa.字段1=(select pal.字段1 from 表1 pal where pal.字段2=pa.字段2 and pal.字段3=pa.字段3 limit 1 )
```

数据表迁移
---------
```sql
//货品明细规格详情
CREATE OR REPLACE FUNCTION "test"."transfer_goods_value_spec_data"()
  RETURNS "pg_catalog"."void" AS $BODY$
declare 
goods_id integer;
--value_id integer;
prod_attr_id integer;
value varchar(144);
begin
  for goods_id,  prod_attr_id,value
	in(select t.goods_id as "goods_id",  t.prod_attr_id as "prod_attr_id" ,t.value as "value"
	from 
	dblink('host=120.76.214.182  port=5432 dbname=lzy201808 user=odoo password=odoo',
	'SELECT pp.id as goods_id,pal.id as prod_attr_id,pav.name as value FROM product_attribute_value_product_product_rel pavpp left join product_product pp on pp.id =pavpp.prod_id left join product_attribute_value pav on pav.id = pavpp.att_id left join product_attribute pa on pa.id = pav.attribute_id left join product_attribute_line pal on pal.product_tmpl_id =pp.product_tmpl_id and pal.attribute_id = pav.attribute_id where pa.type = 1 ORDER BY pp.id asc
	
	') as t (goods_id int,prod_attr_id int ,value VARCHAR ))
loop
		INSERT INTO "test"."prod_goods_value" ("goods_id", "prod_attr_id","attr_value","attr_type") 
		VALUES (goods_id, prod_attr_id,value,1);
END loop;
END;  
$BODY$
  LANGUAGE plpgsql VOLATILE
  COST 100
```
postgresql
-------



mysql使用GROUP_CONCAT子查询多字符返回
需求描述
------
一个订单有多个类型，数据库保存type_ids字段为多个id用，隔开，查询某个订单所属类型详情时，返回类型对应的名称，即返回type_names为多个名称用，隔开。
解决方案
```sql
SELECT i.id, order_id,type_ids,
(SELECT GROUP_CONCAT(t.`name`) FROM t_gas_type t WHERE FIND_IN_SET(t.id,type_ids)) AS type_names
FROM t_inspect i
```
在一开始的应用中，使用了如下的解决方案，但是结果返回不正确。
```sql
 SELECT i.id, order_id,gas_type_ids,
(SELECT GROUP_CONCAT(t.`name`) FROM t_gas_type t WHERE t.id in(gas_type_ids)) AS gas_type_names
FROM t_inspect i
```

mysql转换表类型为utf8mb4生成语句
------

SELECT CONCAT('ALTER TABLE ',TABLE_NAME,' CONVERT TO CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;') from information_schema.TABLES WHERE TABLE_SCHEMA = 'dms_huizhou';

获取表结构字段
-----
select * from INFORMATION_SCHEMA.COLUMNS 
where table_schema ='ums_ecology'

mysql分页查询优化
-----
原sql
```sql
select *  from  test order by id limit 5,10;
```
优化后sql
```sql
select * 
from test where id >=(select id from test order by id desc limit 40,1) 
limit 10
```
或
```sql
SELECT *
FROM test
INNER JOIN (SELECT id FROM test ORDER BY id LIMIT 50, 5)
USING (id)
```
