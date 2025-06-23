4. 

EXPLAIN ANALYZE
SELECT b.start_year, COUNT(*) AS nb_films, AVG(r.average_rating) AS avg_rating
FROM title_basics b
JOIN title_ratings r ON b.tconst = r.tconst
WHERE b.title_type = 'movie'
  AND b.start_year BETWEEN 1990 AND 2000
GROUP BY b.start_year
ORDER BY avg_rating DESC;

4.2
 Oui le résultat est clair
Car le volume est énorme (environ 4m lignes)
J’ai pas l’impression que les index soient utilisées, il doit avoir une méthode plus rapide
ça m’a pas l’air couteux mais j’ai pas de point de repère 

4.3 
CREATE INDEX idx_basics_tconst ON title_basics(tconst);
CREATE INDEX idx_ratings_tconst ON title_ratings(tconst);

4.4
c’est 2x plus rapide
4.5
L’index n’est pas utile car la jointure est pas hyper restrictive 
Il doit trouver ça plus opti
Sur un filtre ultra restrictif 
