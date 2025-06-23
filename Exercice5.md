5.1

<img width="471" alt="image" src="https://github.com/user-attachments/assets/f1a11dfa-0027-45d8-883b-9e046f5103d5" />

<img width="471" alt="image" src="https://github.com/user-attachments/assets/91794efc-f18a-4017-8953-3b388944582f" />



5.2

1. Quel algorithme de jointure est utilisé cette fois ? 
PostgreSQL utilise un Nested Loop car il recherche une seule ligne spécifique dans chaque table grâce à la clé primaire.notre situation avec la recherche d'un identifiant unique.

2. Comment les index sur tconst sont-ils utilisés ? 
PostgreSQL exploite deux clés primaires : 
•	title_basics_pkey pour la table title_basics 
•	title_ratings_pkey pour la table title_ratings. 
Ces index permettent un accès direct avec Index Cond: ((tconst)::text = 'tt0111161'::text)

3. Comparez le temps d'exécution avec les requêtes précédentes 
Cette requête s’exécute en 2,007 millisecondes, alors que la requête précédente prenait 655 millisecondes.
Cela représente une amélioration de 99,69 % en termes de temps d’exécution.


4. Pourquoi cette requête est-elle si rapide ?
Pour plusieurs raisons :
•	une recherche ponctuelle par clé primaire
•	qu’il n’y ait qu’une seule ligne retournée par table (rows=1)
•	les Index utilisés pour avoir un accès direct
