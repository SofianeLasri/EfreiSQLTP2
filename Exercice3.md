3.1 

EXPLAIN ANALYZE
SELECT b.primary_title, r.average_rating
FROM title_basics b
JOIN title_ratings r ON b.tconst = r.tconst
WHERE b.title_type = 'movie'
  AND b.start_year = 1994
  AND r.average_rating > 8.5;

CREATE INDEX idx_average_rating ON title_ratings(average_rating);

3.2
C’est du hash join
En seqScan
Elle est traitée a la fin de la requete en dernier
Quand le résultat de la requête contient trop de lignes

3.3 
CREATE INDEX idx_average_rating ON title_ratings(average_rating);

3.5 
Nested Loop
Il n’est pas utilisé entièrement car comme dit avant le filtre est fait a la fin
Oui il parcourt moins de ligne
Car l’index est très rapide
