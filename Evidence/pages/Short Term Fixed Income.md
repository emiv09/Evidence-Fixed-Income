## Short Term Fixed Income

```sql short_term_bonds_observations
SELECT 
    strptime(strftime(recorded_at, '%Y-%m'), '%Y-%m') AS recorded_at,
    yield,
    short_term_income.observations.series_id as series_id,
    title
FROM short_term_income.observations
JOIN short_term_income.series_info ON short_term_income.observations.series_id = short_term_income.series_info.series_id
WHERE recorded_at < ${last_month}

```

```sql last_month
SELECT strptime(strftime(MAX(recorded_at), '%Y-%m'),'%Y-%m') as last_month FROM short_term_income.observations
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



```sql current_yield
select 
    title,  
    series_id, 
    last(yield) as current_yield 
from ${short_term_bonds_observations}
group by series_id, title
order by current_yield asc
```

### Short Term Fixed Income - Current Yield Histogram

<Histogram 
data={current_yield} 
x=current_yield
xAxisTitle="Yield (%)"
yAxisTitle="Count"
/>

### Short Term Fixed Income - Current Yield

<BarChart
    data={current_yield}
    x=title
    y=current_yield
    sort=false
    xAxisLabels=false
    xAxisTitle = "Bonds"
/>


















