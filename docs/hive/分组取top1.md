### 表格式

vender_code（string） login_name(string)  score(double) 

### 需求：按照vender_code分组，取每组内分数最高的login_name

```mysql
#正确的
select
	*
from
	(
		select
			vender_code,
			login_name,
			score,
			row_number() over(partition by vender_code order by score desc) rank
		from
			tmp_health_item_overview
		where
			generate_date = '2018-08-15 00:00:00.0'
	) last
where
	last.rank = 1 limit 10
	
#错误的 Invalid table alias or column reference 'rank'
select
	vender_code,
	login_name,
	score,
	row_number() over(partition by vender_code order by score desc) rank
from
	tmp_health_item_overview
where
	generate_date = '2018-08-15 00:00:00.0'
	and rank = 1;
```

