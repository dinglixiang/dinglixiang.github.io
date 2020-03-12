---
title: "PostgreSQL: 插入大量模拟数据"
layout: post
date: 2020-03-12 21:57
guid: urn:uuid:dd099863-cd86-4682-af51-fbb428931ec7
author: "Lixiang"
tags:
  - PostgreSQL
  - 转载
---
最近一个业务需求，数据量较大，且查询相当复杂。要针对查询语句测试性能，需要往数据库中批量插入1000万条数据，从而验证查询语句的可行性。


### 1.准备数据
创建两张表`count_group`和`person_face_flow`

```sql
-- DROP TABLE public.count_group;

CREATE TABLE public.count_group
(
    id character varying COLLATE pg_catalog."default" NOT NULL,
    name character varying(64) COLLATE pg_catalog."default" NOT NULL,
    type integer NOT NULL,
    is_delete integer NOT NULL DEFAULT 0,
    area integer,
    create_time timestamp with time zone,
    update_time timestamp with time zone,
    region_id character varying(48) COLLATE pg_catalog."default" NOT NULL,
    CONSTRAINT count_group_pkey PRIMARY KEY (id)
)

-- DROP TABLE public.person_face_flow;

CREATE TABLE public.person_face_flow
(
    id character varying(36) COLLATE pg_catalog."default" NOT NULL,
    group_id character varying(36) COLLATE pg_catalog."default" NOT NULL,
    stat_time timestamp without time zone,
    create_time timestamp without time zone NOT NULL,
    update_time timestamp without time zone NOT NULL,
    in_number integer,
    out_number integer,
    gender character varying(20) COLLATE pg_catalog."default",
    age_type character varying(20) COLLATE pg_catalog."default",
    CONSTRAINT person_face_flow_pkey PRIMARY KEY (id)
)
```

### 2.生成模拟数据的脚本
`count_group`表插入400条模拟数据

```sql
create extension IF NOT EXISTS "uuid-ossp";
DO $$

DECLARE formatStr text := 'INSERT INTO count_group (id, name, type, is_delete,
area,region_id,create_time,update_time)values
((SELECT uuid_generate_v4()), ''统计组_''||''%s'',''%s'',0,''%s'',uuid_generate_v4(),now(),now())';
  DECLARE exe_sql text;

  DECLARE random_base INTEGER := 100;
  DECLARE i int :=1;
  DECLARE gender_array text[] := '{0,1,2,3}';
  DECLARE gender text;

BEGIN
  WHILE i<101 LOOP

    FOREACH gender IN Array gender_array LOOP
        exe_sql = format(formatStr,i,gender,random_base);
        execute exe_sql;
      END LOOP;

    i:=i+1;
  END LOOP;
END
$$;
```

`person_face_flow`表插入模拟数据

```sql
create extension IF NOT EXISTS "uuid-ossp";
DO $$

DECLARE formatStr text := 'INSERT INTO person_face_flow (id, group_id, stat_time, create_time,
update_time,in_number,out_number,gender,age_type)
SELECT uuid_generate_v4(),''%s'',generate_series(''%s''::TIMESTAMPtz,''%s''::TIMESTAMPtz,''1 day'')
,now(),now(),''%s'',''%s'',''%s'',''%s'';';
DECLARE exe_sql text;

DECLARE start_time TIMESTAMPTZ := '2019-01-01' ;
DECLARE end_time TIMESTAMPTZ := '2019-02-01';
DECLARE temp_end TIMESTAMPTZ;
DECLARE is_random BOOLEAN := true;
DECLARE random_base INTEGER := 100;
DECLARE month_time TIMESTAMPTZ;
DECLARE month_time_end TIMESTAMPTZ;
DECLARE record_item RECORD;

DECLARE gender_array text[] := '{male,female,unknow}';
DECLARE gender text;
DECLARE age_array text[] := '{child,youth,old}';
DECLARE age text;

BEGIN
	FOR record_item IN (SELECT id FROM count_group where is_delete=0) LOOP
        FOREACH gender IN Array gender_array LOOP
            FOREACH age IN Array age_array LOOP
                month_time = start_time;
                month_time_end = end_time;
                while month_time < month_time_end LOOP
                    temp_end = month_time + interval '1 week' - interval '1 day';
                    if(temp_end > end_time) then
                        temp_end = end_time;
                    end if;
                    exe_sql = format(formatStr,record_item.id,month_time,temp_end,
                    (random()*100)::int , (random()*100)::int,gender,age);
                    execute exe_sql;
                    month_time = month_time + interval '1 week';
                END LOOP;
            END LOOP;
        END LOOP;
	END LOOP;
END
$$;
```

### 3.总结说明

1. 生成序列
```sql
	SELECT generate_series(1,10)
```
`generate_series`函数

函数| 参数类型 |	返回类型 | 描述
`generate_series(start, stop)` | int 或 bigint | setof int 或 setof bigint(与参数类型相同)	| 生成一个数值序列，从start 到 stop，步进为一
`generate_series(start, stop, step)` | int 或 bigint | setof int 或 setof bigint(与参数类型相同)	| 生成一个数值序列，从start 到 stop，步进为step
`generate_series(start, stop, step_interval)` | timestamp or timestamp with time zone |	timestamp 或 timestamp with time | 	生成一个数值序列，从start 到 stop，步进为step

2. 生成字符串
生成36位的UUID字符串
```sql
	SELECT uuid_generate_v4()
```

生成32位随机字符串
```sql
	SELECT md5(random()::text)
```

生成重复字符串
```sql
	-- abc重复多次的字符串
	SELECT repeat('abc',(random()*4)::integer);
	-- 重复2次的MD5字符串
	SELECT repeat(md5(random()::text),2);
```

连接符
```sql
	SELECT concat('a', ' ', 'b');
```

3. 生成随机数
生成100以内的随机数字
```sql
	SELECT (random()*100)::int
```

生成100以内，总位数为4，小数点后2位的小数
```sql
	SELECT (random()*100.)::numeric(4,2);
```

4. 生成日期
```sql
SELECT date(generate_series(now(), now() + '1 week', '1 day'));
SELECT generate_series(to_date('20130403','yyyymmdd'), t
	o_date('20130404','yyyymmdd'), '3 hours');
```
------------------------
 2013-04-03 00:00:00+08
 2013-04-03 03:00:00+08
 2013-04-03 06:00:00+08
 2013-04-03 09:00:00+08
 2013-04-03 12:00:00+08
 2013-04-03 15:00:00+08
 2013-04-03 18:00:00+08
 2013-04-03 21:00:00+08
 2013-04-04 00:00:00+08
(9 rows)

5. 自定义函数
生成随机汉字
```sql
	create or replace function gen_hanzi(int) returns text as $$
	declare
	  res text;
	begin
	  if $1 >=1 then
	    select string_agg(chr(19968+(random()*20901)::int),'') into res from generate_series(1,$1);
	    return res;
	  end if;
	  return null;
	end;
	$$ language plpgsql strict;
	--输出汉字
	SELECT gen_hanzi(10) FROM generate_series(1,10);
```

生成随机身份证号
```sql
create or replace function gen_id(a date, b date)
returns text as $$
SELECT lpad((random()*99)::int::text, 2, '0') ||
       lpad((random()*99)::int::text, 2, '0') ||
       lpad((random()*99)::int::text, 2, '0') ||
       to_char(a + (random()*(b-a))::int, 'yyyymmdd') ||
       lpad((random()*99)::int::text, 2, '0') ||
       random()::int ||
       (case when random()*10 >9 then 'X' else (random()*9)::int::text end ) ;
$$ language sql strict;

--输出身份证号
 SELECT gen_id('1900-01-01', '2017-10-16') FROM generate_series(1,10);
```
--------------------
25614020061108330X<br>
 49507919010403271X<br>
 96764619970119860X<br>
 915005193407306113<br>
 551360192005045415<br>
 430005192611170108<br>
 299138191310237806<br>
 95149919670723980X<br>
 542053198501097403<br>
 482334198309182411<br>
(10 rows)

生成随机数组

```sql
create or replace function gen_rand_arr(int,int) returns int[] as $$
  select array_agg((random()*$1)::int) from generate_series(1,$2);
$$ language sql strict;
--输出数组
SELECT gen_rand_arr(100,10) FROM generate_series(1,10);
```
---------------------------------
 {69,11,12,70,7,41,81,95,83,17}<br>
 {26,79,20,21,64,64,51,90,38,38}<br>
 {3,64,46,28,26,55,39,12,69,76}<br>
 {66,38,87,78,8,94,18,88,89,1}<br>
 {6,14,81,26,36,45,90,87,35,28}<br>
 {25,38,91,71,67,17,26,5,29,95}<br>
 {82,94,32,69,72,40,63,90,29,51}<br>
 {91,34,66,72,60,1,17,50,88,51}<br>
 {77,13,89,69,84,56,86,10,61,14}<br>
 {5,43,8,38,11,80,78,74,70,6}<br>
(10 rows)

FYI:
- [postgresql插入大量模拟数据总结](https://www.cnblogs.com/seasail/p/12179348.html)
- [postgresql生成随机字符串](http://www.vzhima.com/2020/03/10/postgresql-generate-a-random-string.html)
