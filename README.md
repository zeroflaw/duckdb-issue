# duckdb-issue

```bash
duckdb -c "SELECT time_bucket(INTERVAL '5m', timestamp) AS open_timestamp, FIRST(open) as open, MAX(high) as max, MIN(low) as min, LAST(close) as close FROM (SELECT * FROM 'kline_sample.parquet' ORDER BY timestamp ASC) GROUP BY open_timestamp ORDER BY open_timestamp ASC"
```

The above query flip-flips between the expected result (see the close column):

```
┌──────────────────────────┬──────────────────┬──────────────────┬──────────────────┬──────────────────┐
│      open_timestamp      │       open       │       max        │       min        │      close       │
│ timestamp with time zone │  decimal(24,12)  │  decimal(24,12)  │  decimal(24,12)  │  decimal(24,12)  │
├──────────────────────────┼──────────────────┼──────────────────┼──────────────────┼──────────────────┤
│ 2021-01-01 00:00:00+00   │ 123.600000000000 │ 123.600000000000 │ 123.300000000000 │ 123.500000000000 │
└──────────────────────────┴──────────────────┴──────────────────┴──────────────────┴──────────────────┘
```

and the incorrect result (see the close column):

```
┌──────────────────────────┬──────────────────┬──────────────────┬──────────────────┬──────────────────┐
│      open_timestamp      │       open       │       max        │       min        │      close       │
│ timestamp with time zone │  decimal(24,12)  │  decimal(24,12)  │  decimal(24,12)  │  decimal(24,12)  │
├──────────────────────────┼──────────────────┼──────────────────┼──────────────────┼──────────────────┤
│ 2021-01-01 00:00:00+00   │ 123.400000000000 │ 123.600000000000 │ 123.300000000000 │ 123.300000000000 │
└──────────────────────────┴──────────────────┴──────────────────┴──────────────────┴──────────────────┘
```
