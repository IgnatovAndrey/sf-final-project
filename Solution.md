Задание 1
**Расчет rolling retention с разбивкой по когортам**
```sql
with a as (
	select user_id, 
		   to_char(date_joined, 'YYYY-MM') as dat,
		   date(entry_at) - date(date_joined) as n_days
		from users u 
		join userentry ue 
			on (u.id = ue.user_id)
	    where to_char(date_joined, 'YYYY') = '2022'
), b as (
select dat, count(distinct case when n_days >= 0 then user_id end) as day0
	      from a
	      group by dat
)
select c.dat as yw,
	   round(100.0 * count(distinct case when n_days >= 1 then user_id end) / day0, 2) as day1,
	   round(100.0 * count(distinct case when n_days >= 3 then user_id end) / day0, 2) as day3,
	   round(100.0 * count(distinct case when n_days >= 7 then user_id end) / day0, 2) as day7,
	   round(100.0 * count(distinct case when n_days >= 14 then user_id end) / day0, 2) as day14,
	   round(100.0 * count(distinct case when n_days >= 30 then user_id end) / day0, 2) as day30,
	   round(100.0 * count(distinct case when n_days >= 60 then user_id end) / day0, 2) as day60,
	   round(100.0 * count(distinct case when n_days >= 90 then user_id end) / day0, 2) as day90 
from a c
	join b
		on (c.dat = b.dat)
	group by c.dat, day0

