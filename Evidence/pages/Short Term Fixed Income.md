## Short Term Fixed Income

```sql short_term_bonds_observations
SELECT strptime(strftime(recorded_at, '%Y-%m'), '%Y-%m') AS recorded_at,
 yield,
 series_id 
FROM short_term_income.observations
```

<LineChart
    data={short_term_bonds_observations}
    x=recorded_at
    y=yield
    series =series_id
/>

```sql pie_query
select sector as pie, count(*) as count from short_term_income.series_info
group by sector
```

```sql pie_data
select pie as name, count as value 
from ${pie_query}
```

<ECharts config={
    {
        tooltip: {
            formatter: '{b}: {c} ({d}%)'
        },
        series: [
            {
                type: 'pie',
                data: [...pie_data],
            }
        ]
    }
}/>




















