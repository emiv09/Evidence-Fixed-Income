## Treasury Bonds Rates

```sql dfii10
SELECT date, value, title, notes 
FROM absolute JOIN series_info ON absolute.series_id = series_info.series_id
where absolute.series_id = 'DFII10'
```


<DateRange
    name=range_filtering_a_query
    data={dfii10}
    dates=date
/>

```sql filtered_query
select 
    *
from ${dfii10}
where date between '${inputs.range_filtering_a_query.start}' and '${inputs.range_filtering_a_query.end}'
```

<LineChart
    data={filtered_query}
    x=date
    y=value
/>

<h1 class="text-center"> {dfii10[0].title} </h1>

<LineChart
    data={dfii10}
    x=date
    y=value
/>







