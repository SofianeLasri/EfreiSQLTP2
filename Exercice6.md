1. Quand un index est-il le plus efficace?

        ○ Pour les recherches ponctuelles ou volumineuses?
        pour les recherches ponctuelles

        ○ Pour les colonnes avec forte ou faible cardinalité?
        avec forte cardinalité

       ○ Pour les opérations de type égalité ou intervalle?
        de type égalité


2. Quels algorithmes de jointure avez-vous observés?
Nested Loop et Hash Join

        ○ Dans quels contextes chacun est-il préféré?
        Nested Loop pour les petites tables ou des recherches très sélectives avec index
        Hash Join pour les plus grosses tables ou sans index

        ○ Comment le volume de données influence-t-il le choix?
        Plus le volume est grand, plus Hash Join est l'algorithme optimal

3. Quand le parallélisme est-il activé?

        ○ Quels types d'opérations en bénéficient le plus?

        ○ Pourquoi n'est-il pas toujours utilisé?

4. Quels types d'index utiliser dans les cas suivants:

        ○ Recherche exacte sur une colonne
        Index simple
 
        ○ Filtrage sur plusieurs colonnes combinées
        Index composite

        ○ Tri fréquent sur une colonne
        Index simple

        ○ Jointures fréquentes entre tables
        Index sur clés primairess ou étrangères
