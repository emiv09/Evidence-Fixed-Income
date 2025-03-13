## Treasury Bonds Rates

```sql dfii10
SELECT date, value, title, notes 
FROM absolute JOIN series_info ON absolute.series_id = series_info.series_id
where absolute.series_id = 'DFII10'
```


<h1 class="text-center"> {dfii10[0].title} </h1>

<LineChart
    data={dfii10}
    x=date
    y=value
/>

<DateRange
    name=range_filtering_a_query
    data={dfii10}
    dates=date
/>

```sql filtered_query
select 
    *
from ${dfii10}
where date between 
(DATE '${inputs.range_filtering_a_query.start}') 
and 
(DATE '${inputs.range_filtering_a_query.end}' + INTERVAL 1 DAY)
```

<LineChart
    data={filtered_query}
    x=date
    y=value
/>

```sql query_name
WITH MonthlyMax AS (
    SELECT 
        series_id, 
        MAX(date) AS last_day_of_month
    FROM absolute
    GROUP BY series_id, strftime('%Y-%m', date) -- Grupăm după series_id și lună-an
)
SELECT 
    a.series_id, 
    a.date, 
    a.value, 
FROM absolute a
JOIN MonthlyMax m 
    ON a.series_id = m.series_id 
    AND a.date = m.last_day_of_month
JOIN series_info s 
    ON a.series_id = s.series_id
ORDER BY a.date
```


<LineChart
    data={query_name}
    x=date
    y=value
    series = series_id
    title = title
/>

```sql series_info
select series_id, title from series_info
```

<DataTable data={series_info}/>


<BarChart data={dfii10} x=date y=value/>



```sql long_term_bonds_table
SELECT * from Long_Term_Bonds.series_info
ORDER BY Long_Term_Bonds.series_info.sector

```

```sql long_term_bonds_observations
SELECT strptime(strftime(recorded_at, '%Y-%m'), '%Y-%m') AS recorded_at,
 yield,
 series_id 
FROM Long_Term_Bonds.observations

```

<DataTable data={long_term_bonds_table}/>

<LineChart
    data={long_term_bonds_observations}
    x=recorded_at
    y=yield
    series =series_id
/>


```sql long_term_bonds_observation_table
SELECT * from Long_Term_Bonds.observations
WHERE series_id = 'AAA'
```

<DataTable data={long_term_bonds_observation_table}/>
<LineChart
    data={long_term_bonds_observation_table}
    x=recorded_at
    y=yield
/>


















