# 函数

## 聚合函数

```sql
```

## 统计分析函数

```sql
-- 求和
sum()
-- 计数
count
-- 条件语句
decode(条件,值1,返回值1,值2,返回值2,...值n,返回值n,缺省值)
-- 如果当前行是由rollup或者cube汇总得来的，结果就返回1，反之返回0
grouping(column)


group by A,B,C
-- (A,B,C) UNION (A,B) UNION (A) UNION (ALL)
group by rollup(A,B,C)
-- (A,B,C) UNION (A,B) UNION (A,C) UNION (A) UNION (B,C) UNION (B) UNION (C) UNION (ALL)
group by cube(A,B,C)
```

例子

```sql
CREATE TABLE earnings -- 打工赚钱表
(
	earnmonth VARCHAR2 (6),
	-- 打工月份
	area VARCHAR2 (20),
	-- 打工地区
	sno VARCHAR2 (10),
	-- 打工者编号
	sname VARCHAR2 (20),
	-- 打工者姓名
	TIMES INT,
	-- 本月打工次数
	singleincome NUMBER (10, 2),
	-- 每次赚多少钱
	personincome NUMBER (10, 2)
	-- 当月总收入
)
-- 1. 按照月份，统计每個地区的总收入
select earnmonth,area, sum (personincome) from earnings group by earnmonth,area;
-- 2. 按照月份，地区统计收入;即在1的基础上多了每个月份的总收入,和所有汇总总收入的
select earnmonth,area, sum (personincome) from earnings group by rollup (earnmonth, area);
-- 3. 按照月份，地区进行收入总汇总;即在2的基础上增加了各地区的汇总总收入; nulls last 把空值放在最后
select earnmonth,area,sum (personincome) from earnings group by cube (earnmonth, area) order byearnmonth,area nulls last;
-- 4. NULL 值可读性优化
select decode (grouping (earnmonth),1,'所有月份',earnmonth) 月份,decode (grouping (area),1,'全部地区',area) 地区,sum (personincome) 总金额 from earnings group by cube (earnmonth, area) order by earnmonth,area nulls last;
```

view-source:https://test.chnenergybidding.com.cn/bidcswj/epointzbtool_bs/pages/pbprepare/ChuBu_CanShuSZ?BiaoDuanGuid=970c2887-9f12-4f00-b63a-886b68abd774&ClientGuid=41e21313-2cbf-4f2a-9938-699dfe04619e&FileType=ZB&FileVersionGuid=bcbc2d2d-6e1b-4547-afd1-eabe3fd8f363&PVI=&AuditStatus=3&PFDType=5&_t=67356&_winid=w5897

view-source:https://test.chnenergybidding.com.cn/bidcswj/epointzbtool_bs/pages/pbprepare/ChuBu_CanShuSZ?BiaoDuanGuid=970c2887-9f12-4f00-b63a-886b68abd774&ClientGuid=41e21313-2cbf-4f2a-9938-699dfe04619e&FileType=ZB&FileVersionGuid=bcbc2d2d-6e1b-4547-afd1-eabe3fd8f363&PVI=&AuditStatus=3&PFDType=4&_t=9503&_winid=w5897

view-source:https://test.chnenergybidding.com.cn/bidcswj/epointzbtool_bs/pages/pbprepare/ChuBu_CanShuSZ?BiaoDuanGuid=970c2887-9f12-4f00-b63a-886b68abd774&ClientGuid=41e21313-2cbf-4f2a-9938-699dfe04619e&FileType=ZB&FileVersionGuid=bcbc2d2d-6e1b-4547-afd1-eabe3fd8f363&PVI=&AuditStatus=3&PFDType=6&_t=539798&_winid=w5897


view-source:https://test.chnenergybidding.com.cn/bidcswj/epointzbtool_bs/pages/pbprepare/PFD_List?PFDType=9&BiaoDuanGuid=970c2887-9f12-4f00-b63a-886b68abd774&ClientGuid=41e21313-2cbf-4f2a-9938-699dfe04619e&FileType=ZB&FileVersionGuid=bcbc2d2d-6e1b-4547-afd1-eabe3fd8f363&PVI=&AuditStatus=3&_t=840275&_winid=w9910