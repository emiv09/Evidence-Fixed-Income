## Long Term Fixed Income

```sql long_term_bonds_observations
SELECT 
    strptime(strftime(recorded_at, '%Y-%m'), '%Y-%m') AS recorded_at,
    yield,
    long_term_income.observations.series_id as series_id,
    title
FROM long_term_income.observations
JOIN long_term_income.series_info ON long_term_income.observations.series_id = long_term_income.series_info.series_id
WHERE recorded_at < ${last_month}

```

```sql last_month
SELECT strptime(strftime(MAX(recorded_at), '%Y-%m'),'%Y-%m') as last_month FROM long_term_income.observations
```


<LineChart
    data={long_term_bonds_observations}
    x=recorded_at
    y=yield
    series = series_id
/>

```sql pie_query
select sector as pie, count(*) as count from long_term_income.series_info
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


<!-- <Histogram data={query_name} x=column/> -->


```sql current_yield
select 
    title,  
    series_id, 
    last(yield) as current_yield 
from ${long_term_bonds_observations}
group by series_id, title
order by current_yield asc
```

### Long Term Fixed Income - Current Yield Histogram

<Histogram 
data={current_yield} 
x=current_yield
xAxisTitle="Yield (%)"
yAxisTitle="Count"
/>

### Long Term Fixed Income - Current Yield

<BarChart
    data={current_yield}
    x=title
    y=current_yield
    sort=false
/>































