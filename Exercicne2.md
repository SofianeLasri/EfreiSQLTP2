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

On peut observer une réduction drastique du nombre d'I/O et une précision accrue dès le premier filtre. Le temps d'exécution est également réduit (~18 ms), mais plus faiblement.

## 2.5 Impact du nombre de colonnes

Requête :

```sql
EXPLAIN ANALYZE
SELECT tconst, primary_title, start_year, title_type
FROM title_basics
WHERE title_type = 'movie' 
AND start_year = 1950;
```

Résultat :

```
"QUERY PLAN"
"Bitmap Heap Scan on title_basics  (cost=11.88..2782.31 rows=726 width=44) (actual time=0.227..2.026 rows=2009 loops=1)"
"  Recheck Cond: ((start_year = 1950) AND ((title_type)::text = 'movie'::text))"
"  Heap Blocks: exact=933"
"  ->  Bitmap Index Scan on idx_title_basics_start_year_title_type  (cost=0.00..11.70 rows=726 width=0) (actual time=0.130..0.131 rows=2009 loops=1)"
"        Index Cond: ((start_year = 1950) AND ((title_type)::text = 'movie'::text))"
"Planning Time: 0.106 ms"
"Execution Time: 2.133 ms"
```

### Le temps d'exécution a-t-il changé?

Oui le temps a légèrement augmenté.

### Pourquoi cette optimisation est-elle plus ou moins efficace que dans l'exercice 1?

La table était déjà optimisée grâce à l'index composite. La marge de manoeuvre étant relativement petite, l'optimisation n'est pas très importante.

### Dans quel cas un "covering index" serait idéal ?

Ce genre d'index serait idéal dans le cas où nous effectuons les mêmes requêtes beaucoup de fois, comme par exemple pour un tableau de bord affichant des statistiques.

## 2.6 Analyse de l'amélioration globale

### Quelle est la différence de temps d'exécution par rapport à l'étape 2.1?

La différence de temps d'exécution est d'environ 18ms.

### Comment l'index composite modifie-t-il la stratégie?

L'index composition permet une meilleure cartographie des données de la table, permettant un tri plus rapide. C'est comme si nous combinions 2 requêtes de filtres en une seule.

### Pourquoi le nombre de blocs lus ("Heap Blocks") a-t-il diminué?

La précision des requêtes étant bien plus élevée, le nombre de blocs lus se trouve naturellement diminuée.

### Dans quels cas un index composite est-il particulièrement efficace?

Dans les cas où nous avons constamment besoin de filtrer un certain de nombre de fois une donnée, l'usage d'indexes composites peut être utile. Par exemple, recherches de recettes de cuisines d'un certain type et écrites à une certaine période.