---
title: Oracle入门
date: 
categories:
- [SQL]
---

[TOC]

# 表操作

## sequence和表默认值

创建基于类型sequence默认值的表

```sql
create sequence seq_person_id
minvalue 1
maxvalue 999999999999999999
start with 1
increment by 1;

create table person(
person_id number default seq_person_id.nextval,
person_name varchar2(20) default '');
```



# 基本查询

##  `to_char`

```sql
select to_char(sysdate,'DD-MON-YYYY') from dual;
select to_char(sysdate,'MM-DD-YYYY') from dual;
select to_char(sysdate,'yyyy-mm-dd HH:mm:ss') from dual;
```

## `||字符串连接`

```sql
select Rtrim(t.vend_name) || '----' || Rtrim(t.vend_city) || '----' || Rtrim(t.vend_country)  from VENDORS t
```

## 连接查询

```sql
select ( select count(1) from vendors) vendors_all,
         ( select count(1) from products) products_all
from dual;

```

| vendors_all | products_all |
| ----------- | ------------ |
| 6           | 14           |

## `笛卡尔积(交叉连接)`:A表和B表连接查询,无查询条件

```sql
select v.vend_id,v.vend_name, p.vend_id, p.prod_id,p.prod_name  from vendors v,products p
--查询结果工84条记录

select v.vend_id, v.vend_name, p.vend_id, p.prod_name from vendors v, products p
 where v.vend_id = p.vend_id
```

## `内连接(等值连接)`

```sql
 select v.vend_id, v.vend_name, p.vend_id, p.prod_name
  from vendors v inner join  products p
 on v.vend_id = p.vend_id
```

## `自连接`

```sql
-- 查询产品Id为DTNTR 的供应商的所有产品
select p2.prod_id, p2.prod_name
  from products p1, products p2
 where p1.vend_id = p2.vend_id
   and p1.prod_id = 'DTNTR'   
   
select p2.prod_id, p2.prod_name
  from products p1
 inner join products p2
    on p1.vend_id = p2.vend_id
 where p1.prod_id = 'DTNTR'
```

## `左连接`

```sql
--内连接:查询顾客的订单数量
select cus.cust_id, cus.cust_name, ord.order_num
  from customers cus
 inner join orders ord
    on cus.cust_id = ord.cust_id
 order by cus.cust_id
 
 --左连接:查询所有游客的订单,如果没有订单 ,则 order_num为nulll
 select cus.cust_id, cus.cust_name, ord.order_num
  from customers cus
  left join orders ord
    on cus.cust_id = ord.cust_id
 order by cus.cust_id
```

## `UNION组合查询`

```sql
-- union可消除重复行
select vend_id, prod_id,prod_name,prod_price from products where vend_id in (1001,1002)
union 
select vend_id,prod_id,prod_name,prod_price from products where prod_price<5
```

|id| vend_id | prod_id | prod_name | prod_price |
| -------| -------|-------|-------|-------|
|1| 1001 | ANV01 | .5 ton anvil | 5.99 |
|2| 1001 | ANV02 | 1 ton anvil | 9.99 |
|3| 1001 | ANV03 | 2 ton anvil | 14.99 |
|4| 1002 | FU1  | Fuses | 3.42 |
|5| 1002 | OL1  | Oil can | 8.99 |
|6| 1003 | FC   | Carrots | 2.5  |
|7| 1003 | SLING | Sling | 4.49 |
|8| 1003 | TNT1 | TNT (1 stick) | 2.5  |
```sql
-- union all 不会去重
select vend_id, prod_id,prod_name,prod_price from products where vend_id in (1001,1002)
union all
select vend_id,prod_id,prod_name,prod_price from products where prod_price<5
```
|id| vend_id | prod_id | prod_name | prod_price |
| -------| -------|-------|-------|-------|
|1|1001|ANV01|.5 ton anvil|5.99|
|2|1001|ANV02|1 ton anvil|9.99|
|3|1001|ANV03|2 ton anvil|14.99|
|4|1002|OL1|Oil can|8.99|
|***5***|***1002***|***FU1***|***Fuses***|***3.42***|
|***6***|***1002***|***FU1***|***Fuses***|***3.42***|
|7|1003|SLING|Sling|4.49|
|8|1003|TNT1|TNT (1 stick)|2.5|
|9|1003|FC|Carrots|2.5|

## `listagg`

```sql
   SELECT A.ENTERPRISE_ID  ,
                B.LOGON_ID  
                FROM EPAN_ENT_USER_RELATION_REQ  A
                JOIN EPAN_ENT_USER B ON A.ENTERPRISE_ID=B.ENTERPRISE_ID
        WHERE A.STATUS = 'Confirm' 
        AND A.ENTERPRISE_ID < 122
        ORDER BY A.ENTERPRISE_ID
```

|ENTERPRISE_ID	|LOGON_ID|
| -------| -------|
|111|	zhaoliiu1|
|112|	wagnwu2|
|112|	zhaoliiu7|
|114|	zhaoliiu6|
|115|	zhaoliiu5|
|115|	zhaoliiu5|

```sql
select users.ENTERPRISE_ID,listagg(users.LOGON_ID,',')within group(order by users.ENTERPRISE_ID) LOGON_IDS  from   
( 
        SELECT A.ENTERPRISE_ID  ,
                B.LOGON_ID  
                FROM EPAN_ENT_USER_RELATION_REQ  A
                JOIN EPAN_ENT_USER B ON A.ENTERPRISE_ID=B.ENTERPRISE_ID
        WHERE A.STATUS = 'Confirm' 
        AND A.ENTERPRISE_ID < 122
        ORDER BY A.ENTERPRISE_ID
) users group by users.ENTERPRISE_ID
```

|ENTERPRISE_ID |LOGON_IDS|
| -------| -------|
|111 |zhaoliiu1|
|112 |wagnwu2,zhaoliiu7|
|114 |zhaoliiu6|
|115 |zhaoliiu5,zhaoliiu5|

# 用户管理

```sql
select * from all_users
select * from dba_users

select username,sid,serial# from v$session ;
alter system kill session '4,13' 
drop user C##HURUIYI cascade;

create user c##huruiyi identified by huruiyi;
grant dba to c##huruiyi;

revoke UPDATE ANY TABLE from "C##HURUIYI" container=ALL;
revoke CREATE TABLE from "C##HURUIYI" container=ALL;
revoke BACKUP ANY TABLE from "C##HURUIYI" container=ALL;
grant CREATE TABLE to "C##HURUIYI" container=ALL;
grant BACKUP ANY TABLE to "C##HURUIYI" container=ALL;
grant UPDATE ANY TABLE to "C##HURUIYI" container=ALL;

drop user "C##TEST" cascade;

很多时候我们用拥有DBA权限的用户 从oracle数据库导出数据，那么再导入新的数据库时就还得需要DBA权限的用户，下面是如何创建一个新用户并授予DBA权限命令。

1.用有dba权限的用户登录：sys用户

2.创建一个新用户：create user abc identified by 123456;

3.授予DBA权限： grant connect,resource,dba to abc;

ok,创建好了，就可以用abc这个用户登录了，abc用户拥有dba权限。

select * from dba_users; 查看数据库里面所有用户，前提是你是有dba权限的帐号，如sys,system
select * from all_users; 查看你能管理的所有用户！
select * from user_users; 查看当前用户信息 ！

原文地址:https://blog.csdn.net/u012630060/article/
```

# 函数

```sql
create or replace function func_join_strs(str1 varchar2, str2 varchar2)
  return varchar2 IS
begin
  return str1 || '-' || str2;
end;

select func_join_strs('A', 'B') from dual;
```

```sql
--会话级别的变量
variable session_var varchar2(50);
call  func_join_strs('A','B') into :session_var;
select :session_var sv from dual;
```

# 常用函数

## 版本查看

```
select * from v$version;

select * from product_component_version;

select * from v$instance
```



# 触发器

## 触发器动作

 - Alter
 - Create
 - Drop
 - `Delete`
 - `Insert`
 - `Update`



## 特殊触发器

​	Oracle还支持服务器启动和关闭，用户登录和注销上的触发器

## 创建触发器的必要信息

 - 唯一的触发器名称
 - 触发器关联的表
 - 触发器响应的动作(Insert,Update或Delete)
 - 何时执行触发器(响应动作前或后)

## 触发器-Before-Insert(模拟自增主键)

```sql
create table test
(
  id    number not null,
  name  nvarchar2(10) default '' not null,
  isvip char(1)
)
;

comment on column test.name  is '用户名';
  
create sequence SEQ_HURUIYI_TEST
minvalue 1
maxvalue 999999999999999999999999999
start with 1
increment by 1
nocache;
 
select * from all_sequences where sequence_owner='C##HURUIYI'

create or replace trigger TEST_HURUIYI_TRIGGER
  before insert on TEST
  for each row
  when (new.id is null)
begin
  select SEQ_HURUIYI_TEST.nextval into :NEW.ID from dual;
end;
 
select * from user_triggers where trigger_name='TEST_HURUIYI_TRIGGER'
```

## 触发器-After-Insert

 - 创建表

 - ```sql
   create table orders_log(
     changed_on timestamp not null,
     changed_type char(1) not null,
     order_num int not null
   );
   ```

  - 创建存储过程

 - ```
   create or replace trigger orders_after_insert
     after insert on orders
     for each row
   begin
     insert into orders_log
       (changed_on, changed_type, order_num)
     values
       (sysdate, 'A', :new.order_num);
   end;
   ```

  - 插入数据

- ```sql
  insert into orders
    (order_num, order_date, cust_id)
  values
    (50040, sysdate, 10001);
  ```

- 查询执行结果

- ```sql
  select * from orders_log;
  ```

## 触发器-Before-Delete

```sql
create or replace trigger orders_before_delete
  before delete on orders
  for each row
begin
  insert into orders_log
    (changed_on, changed_type, order_num)
  values
    (sysdate, 'D', :old.order_num);
end;
```

```sql
create or replace trigger orders_before_delete
  before delete on orders
  for each row
begin
	-- 删除订单时，插入日志
  insert into orders_log
    (changed_on, changed_type, order_num)
  values
    (sysdate, 'D', :old.order_num);
    -- 删除订单的时候，要存档
  insert into orders_archive
    (order_num, order_date, cust_id)
  values
    (:old.order_num, :old.order_date, :old.cust_id);
end;
```

```sql

delete orders where order_num='50040'
```

```
select * from orders_log
```



# 存储过程

```sql
create or replace procedure Proc_Hello IS
begin
  Dbms_Output.put_line('Hello World');
end;
```

```sql
create or replace procedure PROC_TEST Is
  h number;
  g varchar2(50);
begin
  select extract(hour from current_timestamp) into h from dual;
  if h >= 20 or h <= 5 then
    g := '休息时间';
  elsif h > 5 and h <= 12 then
    g := '上午好';
  elsif h > 12 and h <= 17 then
    g := '下午好';
  else
    g := '晚上好 ';
  end if;
  DBMS_OUTPUT.PUT_LINE(g);
end;
```

```sql
create or replace procedure PROC_TEST(h in number, g out varchar2) as
begin
  if h >= 20 or h <= 5 then
    g := '休息时间';
  elsif h > 5 and h <= 12 then
    g := '上午好';
  elsif h > 12 and h <= 17 then
    g := '下午好';
  else
    g := '晚上好 ';
  end if;
end;
```

## 有参存储过程

```sql
create 
	or replace procedure PROC_ADD (
	v_cust_name in customers.cust_name % type,
	v_cust_address in customers.cust_address % type,
	v_cust_city in customers.cust_city % type,
	v_cust_state in customers.cust_state % type,
	v_cust_zip in customers.cust_zip % type,
	v_cust_country in customers.cust_country % type,
	v_cust_contact in customers.cust_contact % type,
	v_cust_email in customers.cust_email % type 
	) IS v_cust_id number;
begin
select
	max( cust_id ) into v_cust_id 
from
	customers;
v_cust_id := v_cust_id + 1;
insert into customers ( cust_id, cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country, cust_contact, cust_email )
values
	( v_cust_id, v_cust_name, v_cust_address, v_cust_city, v_cust_state, v_cust_zip, v_cust_country, v_cust_contact, v_cust_email );
commit;
end;
```

`执行存储过程`

```sql
begin
	proc_add (
	v_cust_name => 'HuRuiyi',
	v_cust_address => 'ShangHai',
	v_cust_city => 'YangPu',
	v_cust_state => 'CH',
	v_cust_zip => '231000',
	v_cust_country => 'China',
	v_cust_contact => 'ZiHuaXinYuan',
	v_cust_email => '807776962@qq.com' 
	);
commit;
end;
                 
begin
	proc_add ( 'HuRuiyi', 'ShangHai', 'YangPu', 'CH', '231000', 'China', 'ZiHuaXinYuan', '807776962@qq.com' );
commit;
end;   
```

# 游标

`基本的游标应用`

```sql
declare
  --变量声明--
  v_vend_id      vendors.vend_id%type;
  v_vend_name    vendors.vend_name%type;
  v_vend_address vendors.vend_address%type;
  v_vend_city    vendors.vend_city%type;
  v_vend_state   vendors.vend_state%type;
  v_vend_zip     vendors.vend_zip%type;
  v_vend_country vendors.vend_country%type;
  --变量声明--
  cursor cr_test is
    select vend_id,
           vend_name,
           vend_address,
           vend_city,
           vend_state,
           vend_zip,
           vend_country
      from vendors;
begin
  open cr_test;
  loop
    fetch cr_test
      into v_vend_id,
           v_vend_name,
           v_vend_address,
           v_vend_city,
           v_vend_state,
           v_vend_zip,
           v_vend_country;
    exit when cr_test%notfound;
  end loop;
  close cr_test;
end;
```

`游标应用-循环更新`- 批量更新 vend_country = USA的值为 China

```sql
declare
  --变量声明--
  v_vend_id      vendors.vend_id%type;
  v_vend_name    vendors.vend_name%type;
  v_vend_address vendors.vend_address%type;
  v_vend_city    vendors.vend_city%type;
  v_vend_state   vendors.vend_state%type;
  v_vend_zip     vendors.vend_zip%type;
  v_vend_country vendors.vend_country%type;
  --变量声明--
  cursor cr_test is
    select vend_id,
           vend_name,
           vend_address,
           vend_city,
           vend_state,
           vend_zip,
           vend_country
      from vendors;
begin
  open cr_test;
  loop
    fetch cr_test
      into v_vend_id,
           v_vend_name,
           v_vend_address,
           v_vend_city,
           v_vend_state,
           v_vend_zip,
           v_vend_country;
    update vendors
       set vend_country = 'China'
     where vend_id = v_vend_id
       and rtrim(v_vend_country) = 'USA';
    exit when cr_test%notfound;
  end loop;
  close cr_test;
end;
```



# 表复制

`表结构复制`

```sql
create table  vendors_test as select * from vendors where 1=2;
```

`数据导入`

```sql
insert into vendors_test ( vend_id, vend_name, vend_address, vend_city, vend_state, vend_zip, vend_country )
				    select vend_id, vend_name, vend_address, vend_city, vend_state, vend_zip, vend_country from	vendors
```

# 数据的导入与导出



```
exp c##wxsw/wxsw@orcl full=y inctype=complete file=131516.dmp
exp scott/123456@192.168.35.129:1521/ORCL  full=y inctype=complete  file=d:/scott-wxsw.dmp


导出
exp username/password@address/listener file=D:\test\a.dmp
 
导入
imp username/password@host/listener file = D:\xx\xx.dmp log = D:\xx\xx.log full = y
```



# listagg

```sql
 create table TR_RATE_FACILITY_ASSIGN("UUID" CHAR(32) NOT NULL ENABLE, 
  "TRF_GROUP_UUID" CHAR(32), 
  "FCIL_CDE" VARCHAR2(20) NOT NULL ENABLE )
  
  
1	1                               	e1dadd566f894627b26a19ee347ac788	APU02
2	2                               	e1dadd566f894627b26a19ee347ac788	APU01
3	3                               	e1dadd566f894627b26a19ee347ac788	APU03
4	4                               	e1dadd566f894627b26a19ee347ac788	AQG01
5	5                               	9d630d6df637484ab6f966e47a077d00	BHY01
6	6                               	9d630d6df637484ab6f966e47a077d00	BHY02
7	7                               	9d630d6df637484ab6f966e47a077d00	APU02

  
SELECT TRF_GROUP_UUID,
    listagg(FCIL_CDE,',') within GROUP(ORDER BY uuid) facility
FROM
    TR_RATE_FACILITY_ASSIGN
GROUP BY
    TRF_GROUP_UUID
    
    
1	9d630d6df637484ab6f966e47a077d00	BHY01,BHY02,APU02
2	e1dadd566f894627b26a19ee347ac788	APU02,APU01,APU03,AQG01
```

# 表字段-注释

```
select t.*
  from ALL_OBJECTS t
 where t.OBJECT_TYPE = 'TABLE'
   and t.OBJECT_NAME = 'BIZ_TASK'
   and t.OWNER = 'C##WXSW';

SELECT distinct t.OBJECT_TYPE from all_objects t;

SELECT * FROM V$VERSION;
select * from ALL_VIEWS;

SELECT * FROM PRODUCT_COMPONENT_VERSION;
SELECT * FROM PRODUCT_PROFILE;
SELECT * FROM PRODUCT_USER_PROFILE;

--表备注信息
select *
  from all_tab_comments t
 where t.OWNER = 'C##WXSW'
   and t.TABLE_NAME = 'BIZ_TASK';

select * from all_tab_comments t where t.TABLE_NAME = 'BIZ_TASK';

Comment on table BIZ_TASK is '表备注信息......';
Comment on table "C##LIUZHE".BIZ_TASK is '表备注信息......';

--表字段信息
select *
  from all_tab_cols t
 where t.OWNER = 'C##WXSW'
   and t.TABLE_NAME = 'BIZ_TASK';

select *
  from all_tab_columns t
 where t.OWNER = 'C##WXSW'
   and t.TABLE_NAME = 'BIZ_TASK';

--表字段注释信息
select *
  from all_col_comments t
 where t.OWNER = 'C##WXSW'
   and t.TABLE_NAME = 'BIZ_TASK';

--表字段注释信息修改
Comment on column Biz_Task.Id is '主键'
```

