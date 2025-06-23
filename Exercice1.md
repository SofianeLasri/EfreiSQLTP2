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

## 1.2 Analyse du plan d'exécution

### Pourquoi PostgreSQL utilise-t-il un Parallel Sequential Scan?

L'absence d'index et le volume important des données explique le choix de PostreSQL d'utiliser un Parallel Sequential Scan.

### La parallélisation est-elle justifiée ici? Pourquoi?

Oui en raison de la quantité importante d'entrées dans la table analysée. La parallélisation permet d'accélérer le processus.

### Que représente la valeur "Rows Removed by Filter"?

Il s'agit du nombre de lignes qui ne correspondent pas aux critères définis par la requêtes, ctd les entrées dont la date de début est différente de 2020.

## 1.3 & 1.4 

La création de cet index a permis une importante accélération du processus d'analyse d'environ 3 secondes.

```
"Gather  (cost=5687.30..271605.46 rows=420499 width=86) (actual time=92.294..638.245 rows=440009 loops=1)"
"  Workers Planned: 2"
"  Workers Launched: 2"
"  ->  Parallel Bitmap Heap Scan on title_basics  (cost=4687.30..228555.56 rows=175208 width=86) (actual time=62.915..539.856 rows=146670 loops=3)"
"        Recheck Cond: (start_year = 2020)"
"        Rows Removed by Index Recheck: 671753"
"        Heap Blocks: exact=10758 lossy=9900"
"        ->  Bitmap Index Scan on idx_title_basics_start_year  (cost=0.00..4582.18 rows=420499 width=0) (actual time=78.999..79.000 rows=440009 loops=1)"
"              Index Cond: (start_year = 2020)"
"Planning Time: 0.445 ms"
"JIT:"
"  Functions: 6"
"  Options: Inlining false, Optimization false, Expressions true, Deforming true"
"  Timing: Generation 1.478 ms (Deform 0.517 ms), Inlining 0.000 ms, Optimization 1.314 ms, Emission 26.303 ms, Total 29.094 ms"
"Execution Time: 667.655 ms"
```

## 1.5 Impact du nombre de colonnes

Requête modifiée :

```sql
EXPLAIN ANALYZE
SELECT tconst, primary_title, start_year
FROM title_basics
WHERE start_year = 2020;
```

Résultat :

```
"Gather  (cost=5687.30..271605.46 rows=420499 width=35) (actual time=60.022..446.708 rows=440009 loops=1)"
"  Workers Planned: 2"
"  Workers Launched: 2"
"  ->  Parallel Bitmap Heap Scan on title_basics  (cost=4687.30..228555.56 rows=175208 width=35) (actual time=37.589..376.451 rows=146670 loops=3)"
"        Recheck Cond: (start_year = 2020)"
"        Rows Removed by Index Recheck: 671753"
"        Heap Blocks: exact=13388 lossy=11798"
"        ->  Bitmap Index Scan on idx_title_basics_start_year  (cost=0.00..4582.18 rows=420499 width=0) (actual time=47.934..47.935 rows=440009 loops=1)"
"              Index Cond: (start_year = 2020)"
"Planning Time: 0.090 ms"
"JIT:"
"  Functions: 12"
"  Options: Inlining false, Optimization false, Expressions true, Deforming true"
"  Timing: Generation 1.250 ms (Deform 0.604 ms), Inlining 0.000 ms, Optimization 1.256 ms, Emission 17.551 ms, Total 20.056 ms"
"Execution Time: 466.752 ms"
```

### Le temps d'exécution a-t-il changé? Pourquoi?

Oui le temps d'exécution a été accéléré en raison du nombre de colonnes sélectionnées moins important.

### Le plan d'exécution est-il différent ?

Non il reste identique.

### Pourquoi la sélection de moins de colonnes peut-elle améliorer les performances?

J'ai envie de dire que ça tombe sous le sens. Mais bon au cas où, on va dire que c'est parce que les chariots sont moins lourds à pousser. (:

## 1.6 Analyse de l'impact global

### Quelle nouvelle stratégie PostgreSQL utilise-t-il maintenant?

Après ajout de l'index, Postgre utilisé Parallel Bitmap Heap Scan  au lieu de Parallel Sequential Scan.

### Le temps d'exécution s'est-il amélioré? De combien?

Comme précédemment expliqué, le temps d'exécution s'est amélioré d'un peu plus de 3 secondes.

### Que signifie "Bitmap Heap Scan" et "Bitmap Index Scan"?

Selon un bon ami savant nommé Gérard Patrick-Théodore (sacré nom je sais), le Bitmap Index Scan crée un bitmap (carte de bits) indiquant quelles pages de la table contiennent des lignes intéressantes et ne récupère pas les données, juste leur position. À l'inverse, le Bitmap Bitmap Heap Scan utilise le bitmap créé par Postgre pour accéder directement aux pages pertinentes de la table. De plus, il récupère les données réelles des lignes.

### Pourquoi l'amélioration n'est-elle pas plus importante ?

Bien que selon moi, l'améliration est déjà conséquente, on peut imaginer qu'elle l'aurait été encore plus avec un nombre de donnée encore plus important et sur une machine plus lente. De plus, le filtrage actuel ne permet pas une exécution particulièrement rapide en raison du grand nombre de données écartées.