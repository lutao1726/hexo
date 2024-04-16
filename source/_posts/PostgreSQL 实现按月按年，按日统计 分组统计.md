---
title: PostgreSQL 实现按月按年，按日统计 分组统计
date: 2018-08-27 09:00:32
categories: SQL教程
tags: [ Java, Mysql, PostgreSql]

---
<!-- more -->
--按年分组查看
>     select     to_char(to_timestamp(start_time_of_date::bigint), 'YYYY') as d ,  count(cdr_id)  as  total_call,sum (call_duration::integer /60 +1)   as  total_duration  from  cdr   where  to_timestamp(start_time_of_date::bigint)  between  '2010-01-01'   and    '2010-12-12'   group by d



--按月分组查看
>     select     to_char(to_timestamp(start_time_of_date::bigint), 'YYYY-MM') as d ,  count(cdr_id)  as  total_call,sum (call_duration::integer /60 +1)   as  total_duration  from  cdr   where  to_timestamp(start_time_of_date::bigint)  between  '2010-01-01'   and    '2010-12-12'   group by d

--按天分组查看
>     select     to_char(to_timestamp(start_time_of_date::bigint), 'YYYY-MM-DD') as d ,  count(cdr_id)  as  total_call,sum (call_duration::integer /60 +1)   as  total_duration  from  cdr   where  to_timestamp(start_time_of_date::bigint)  between  '2010-01-01'   and    '2010-12-12'   group by d


--按小时分组查看
>     select     to_char(to_timestamp(start_time_of_date::bigint), 'YYYY-MM-DD  HH24 ' ) as d ,  count(cdr_id)  as  total_call,sum (call_duration::integer /60 +1)   as  total_duration  from  cdr  where  to_timestamp(start_time_of_date::bigint)  between  '2010-01-01'   and    '2010-12-12'   group by d  order  by  d

--按秒分组查看
>     select     to_char(to_timestamp(start_time_of_date::bigint), 'YYYY-MM-DD  HH24:MI:SS ' ) as d ,  count(cdr_id)  as  total_call,sum (call_duration::integer /60 +1)   as  total_duration  from  cdr   where  to_timestamp(start_time_of_date::bigint)  between  '2010-01-01'   and    '2010-12-12'   group by d