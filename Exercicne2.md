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

## 2.2 Analyse du plan d'exécution

### Comment est traité le filtre sur title_Type?

Le filtre title_type est traité de manière classique, en dehors des indexs.

### Combien de lignes passent le premier filtre, puis le second?

| Étape | Nombre de lignes | Description |
| --- | --- | --- |
| Premier filtre (start_year = 1950) | 8 277 lignes | Trouvées via l'index |
| Lignes rejetées par title_type | 6 268 lignes | "Rows Removed by Filter" |
| Résultat final | 2 009 lignes | Films de 1950 uniquement |

2009 films passent l'ensemble des filtres.

### Quelles sont les limitations de notre index actuel?

Les performances se trouvent affectées en raison du filtrage effectué après la récupération des données. L'ajout d'un index composite permetrait de résoudre ce poroblème de performance.

## 2.3 & 2.4

Résultat après ajout de l'index :

```
"QUERY PLAN"
"Bitmap Heap Scan on title_basics  (cost=11.88..2782.31 rows=726 width=86) (actual time=0.495..1.885 rows=2009 loops=1)"
"  Recheck Cond: ((start_year = 1950) AND ((title_type)::text = 'movie'::text))"
"  Heap Blocks: exact=933"
"  ->  Bitmap Index Scan on idx_title_basics_start_year_title_type  (cost=0.00..11.70 rows=726 width=0) (actual time=0.376..0.376 rows=2009 loops=1)"
"        Index Cond: ((start_year = 1950) AND ((title_type)::text = 'movie'::text))"
"Planning Time: 0.495 ms"
"Execution Time: 1.985 ms"
```

On peut observer une réduction drastique du nombre d'I/O et une précision accrue dès le premier filtre. Le temps d'exécution est également beaucoup réduit (~18 secondes).