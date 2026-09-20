> Copie publique d’un README de J.E.S.S.I. Seuls les README sont publiés ; les liens vers le code, les guides détaillés et les profils nécessitent l’accès au dépôt privé. Aucun de ces fichiers n’est exporté.

# Memory.Contracts

Le [contrat d'interprétation](https://github.com/gilldelia/J.E.S.S.I/blob/1c3e61555b42a389b51847e2ca5cefea6f3f0553/Memory.Contracts/MemoryInterpretation.cs) distingue observation,
hypothèse et incertitude, avec versions et citations bornées. Sa validation
structurelle n'établit ni vérité sémantique ni autorisation. Le champ facultatif
est absent sur les anciennes données ; voir le [socle appliqué et ses limites](https://github.com/gilldelia/J.E.S.S.I/blob/1c3e61555b42a389b51847e2ca5cefea6f3f0553/docs/reconstructive-memory.md).

Les [annotations d'expérience](https://github.com/gilldelia/J.E.S.S.I/blob/1c3e61555b42a389b51847e2ca5cefea6f3f0553/docs/memory-experience.md) complètent les
signaux existants avec l'attribution émotionnelle et les circonstances
d'apprentissage exprimées/interprétées. Leurs champs sont facultatifs ; les
anciens souvenirs restent lisibles sans migration ni émotion inventée.
Les [épisodes relationnels](https://github.com/gilldelia/J.E.S.S.I/blob/1c3e61555b42a389b51847e2ca5cefea6f3f0553/docs/relational-memory.md) suivent la même
séparation déclaration/interprétation, sans accès ou statut déduit de leurs clés.

Ce projet est l'unique propriétaire des types publics échangés avec l'API
Memory. Il est partagé par le service, l'adaptateur MCP et l'ancien client
console afin que les mêmes noms, enums et formes JSON soient compilés partout.

Il ne doit contenir ni transport HTTP, ni accès Qdrant, ni fournisseur
d'embeddings, ni logique métier. Les contrats restent dans l'espace de noms
`Memory.DTOs` pour préserver la compatibilité source lors de cette extraction.

Le [bilan du cycle de sommeil](https://github.com/gilldelia/J.E.S.S.I/blob/1c3e61555b42a389b51847e2ca5cefea6f3f0553/Memory.Contracts/SleepCycleModels.cs) expose par espace des
[diagnostics intuitifs agrégés](https://github.com/gilldelia/J.E.S.S.I/blob/1c3e61555b42a389b51847e2ca5cefea6f3f0553/Memory.Contracts/IntuitiveCompilationDiagnostics.cs), sans texte
ou identifiant de source. Ils sont réservés au bilan de maintenance interne ;
ils ne changent aucun droit OAuth ni seuil de compilation. Les anciens rapports
restent lisibles avec un diagnostic absent (`null`), pas un succès supposé.
Voir les [unités, états et limites du bilan](https://github.com/gilldelia/J.E.S.S.I/blob/1c3e61555b42a389b51847e2ca5cefea6f3f0553/docs/qa/intuitive-diagnostics.md).
