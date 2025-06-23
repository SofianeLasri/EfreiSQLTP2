# Exercice 1

## 1.1 Première analyse

Requête :

```sql
EXPLAIN ANALYZE
SELECT *
FROM title_basics
WHERE start_year = 2020;
```

Résultat :

```
"QUERY PLAN"
"Gather  (cost=1000.00..278036.41 rows=420546 width=86) (actual time=78.284..3314.209 rows=440009 loops=1)"
"  Workers Planned: 2"
"  Workers Launched: 2"
"  ->  Parallel Seq Scan on title_basics  (cost=0.00..234981.81 rows=175228 width=86) (actual time=54.349..3212.952 rows=146670 loops=3)"
"        Filter: (start_year = 2020)"
"        Rows Removed by Filter: 3764950"
"Planning Time: 4.139 ms"
"JIT:"
"  Functions: 6"
"  Options: Inlining false, Optimization false, Expressions true, Deforming true"
"  Timing: Generation 1.939 ms (Deform 0.633 ms), Inlining 0.000 ms, Optimization 6.631 ms, Emission 71.354 ms, Total 79.923 ms"
"Execution Time: 3729.694 ms"
```