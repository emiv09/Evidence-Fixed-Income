# Long Term Fixed Income


```sql current_month
SELECT strptime(strftime(MAX(recorded_at), '%Y-%m'),'%Y-%m') as last_month FROM long_term_income.observations
```

```sql previous_month
SELECT last_month - INTERVAL 1 MONTH as previous_month FROM ${current_month}
```

```sql previous_previous_month
SELECT previous_month - INTERVAL 1 MONTH as previous_previous_month FROM ${previous_month}
```



```sql long_term_bonds_observations
SELECT 
    strptime(strftime(recorded_at, '%Y-%m'), '%Y-%m') AS recorded_at,
    yield,
    long_term_income.observations.series_id as series_id,
    title,
    sector
FROM long_term_income.observations
JOIN long_term_income.series_info ON long_term_income.observations.series_id = long_term_income.series_info.series_id
WHERE recorded_at < ${current_month}

```


<LineChart
    data={long_term_bonds_observations}
    x=recorded_at
    y=yield
    series = title
    xFmt = "mmmm, yyyy"
/>

```sql pie_query
select sector as pie, count(*) as count from long_term_income.series_info
group by sector
```

```sql pie_data
select pie as name, count as value 
from ${pie_query}
```

## Sector breakdown

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


```sql previous_month_yield
select 
    title,  
    series_id, 
    yield as previous_month_yield,
    sector,
    ${previous_month} as previous_month
from ${long_term_bonds_observations}
where recorded_at = ${previous_month}
order by previous_month_yield asc
```
 

## Yield Histogram

This is a histogram for <Value data={previous_month} fmt="mmmm, yyyy"/>, showing how many bonds are in each yield bucket 

<Histogram 
data={previous_month_yield} 
x=previous_month_yield
xAxisTitle="Yield (%)"
yAxisTitle="Count"
/>

## Long-term Fixed Income - monthly yield

<Value data={previous_month} fmt="mmmm, yyyy"/>

<BarChart
    data={previous_month_yield}
    x=title
    y=previous_month_yield
    yAxisTitle="Yield %"
    xAxisTitle="Bond Title"
    xAxisLabels=false
    sort=false
    
/>

```sql table_query
select 
    title,  
    sector, 
    previous_month_yield
from ${previous_month_yield}
order by previous_month_yield asc
```


```sql previous_previous_month_yield
select 
    yield as previous_previous_month_yield, 
    title, 
    sector,
    ${previous_previous_month} as previous_previous_month,
from ${long_term_bonds_observations}
where recorded_at = ${previous_previous_month}
order by previous_previous_month_yield asc
```




```sql query_name
SELECT 
    a.title,
    a.sector,
    a.previous_month_yield,
    b.previous_previous_month_yield,
    strftime('%B %Y', a.previous_month) AS previous_month_label,
    strftime('%B %Y', b.previous_previous_month) AS previous_previous_month_label,
    (a.previous_month_yield - b.previous_previous_month_yield) AS yield_difference
FROM ${previous_month_yield} a
JOIN ${previous_previous_month_yield} b 
ON a.title = b.title
ORDER BY a.title ASC

```


## Long-term Fixed Income - yield comparison

Comparing the yields of <Value data={previous_month} fmt="mmmm, yyyy"/> to <Value data={previous_previous_month} fmt="mmmm, yyyy"/> for each component.

<DataTable data={query_name} groupBy="sector" >
    
    <Column id=title title="Sector and Title"/>
    <Column id=yield_difference title = "Yield Difference" contentType=delta fmt="%.2f"/>
    <Column id=previous_month_yield title = '{(query_name[0].previous_month_label)} (%)'/>
    <Column id=previous_previous_month_yield title = '{(query_name[0].previous_previous_month_label)} (%)'/>
    
</DataTable>








































