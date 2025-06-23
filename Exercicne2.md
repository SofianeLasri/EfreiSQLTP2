# Exercice 2

## 2.1 Requête avec conditions multiples

Requête :

```sql
EXPLAIN ANALYZE
SELECT *
FROM title_basics
WHERE title_type = 'movie' 
AND start_year = 1950;
```

Résultat :

```
"QUERY PLAN"
"Bitmap Heap Scan on title_basics  (cost=125.70..35954.98 rows=726 width=86) (actual time=0.954..20.049 rows=2009 loops=1)"
"  Recheck Cond: (start_year = 1950)"
"  Filter: ((title_type)::text = 'movie'::text)"
"  Rows Removed by Filter: 6268"
"  Heap Blocks: exact=3275"
"  ->  Bitmap Index Scan on idx_title_basics_start_year  (cost=0.00..125.52 rows=11344 width=0) (actual time=0.565..0.565 rows=8277 loops=1)"
"        Index Cond: (start_year = 1950)"
"Planning Time: 0.090 ms"
"Execution Time: 20.177 ms"
```