> Copie publique d’un README de J.E.S.S.I. Seuls les README sont publiés ; les liens vers le code, les guides détaillés et les profils nécessitent l’accès au dépôt privé. Aucun de ces fichiers n’est exporté.

# Memory.Contracts

Le [contrat d'interprétation](https://github.com/gilldelia/J.E.S.S.I/blob/ab631e6d985aa3c618b3a561b250be6c950b8bd7/Memory.Contracts/MemoryInterpretation.cs) distingue observation,
hypothèse et incertitude, avec versions et citations bornées. Sa validation
structurelle n'établit ni vérité sémantique ni autorisation. Le champ facultatif
est absent sur les anciennes données ; voir le [socle appliqué et ses limites](https://github.com/gilldelia/J.E.S.S.I/blob/ab631e6d985aa3c618b3a561b250be6c950b8bd7/docs/reconstructive-memory.md).

La [compréhension long terme](https://github.com/gilldelia/J.E.S.S.I/blob/ab631e6d985aa3c618b3a561b250be6c950b8bd7/docs/long-term-understanding.md) ajoute la
perspective historique bornée et `consolidation` (propos, pertinence, date,
statut et provenance). Depuis #491, le `text` d'une nouvelle consolidation reste
l'observation reçue (rôle `interpreted-observation`, type `Observation`) ; seuls
les reçus historiques de rôle `interpretation` portent une reformulation comme
`text`. Dans tous les cas, `interpretation.observation` est la preuve, jamais le
texte généré.

Les [annotations d'expérience](https://github.com/gilldelia/J.E.S.S.I/blob/ab631e6d985aa3c618b3a561b250be6c950b8bd7/docs/memory-experience.md) complètent les
signaux existants avec l'attribution émotionnelle et les circonstances
d'apprentissage exprimées/interprétées. Leurs champs sont facultatifs ; les
anciens souvenirs restent lisibles sans migration ni émotion inventée.
Les [épisodes relationnels](https://github.com/gilldelia/J.E.S.S.I/blob/ab631e6d985aa3c618b3a561b250be6c950b8bd7/docs/relational-memory.md) suivent la même
séparation déclaration/interprétation, sans accès ou statut déduit de leurs clés.
Les [expériences action→résultat](https://github.com/gilldelia/J.E.S.S.I/blob/ab631e6d985aa3c618b3a561b250be6c950b8bd7/docs/memory-experience.md) (#493) ancrent
action, résultat et cause dans des citations exactes du texte admis ;
`ActionOutcomeContract` porte les règles déterministes que le service et ses
passerelles appliquent à l'identique, sans appel au modèle. Une recherche avec
`procedureKey` ajoute les [leçons de cette procédure](https://github.com/gilldelia/J.E.S.S.I/blob/ab631e6d985aa3c618b3a561b250be6c950b8bd7/Memory.Contracts/ProcedureLesson.cs) :
une vue projetée à la lecture selon `lesson-independence/1`, jamais stockée et
de seule autorité descriptive ; son `scopeId` interne ne sort pas de la passerelle.

Ce projet est l'unique propriétaire des types publics échangés avec l'API
Memory. Il est partagé par le service, l'adaptateur MCP et l'ancien client
console afin que les mêmes noms, enums et formes JSON soient compilés partout.

Il ne doit contenir ni transport HTTP, ni accès Qdrant, ni fournisseur
d'embeddings, ni logique métier. Les contrats restent dans l'espace de noms
`Memory.DTOs` pour préserver la compatibilité source lors de cette extraction.

Le [bilan du cycle de sommeil](https://github.com/gilldelia/J.E.S.S.I/blob/ab631e6d985aa3c618b3a561b250be6c950b8bd7/Memory.Contracts/SleepCycleModels.cs) expose par espace des
[diagnostics intuitifs agrégés](https://github.com/gilldelia/J.E.S.S.I/blob/ab631e6d985aa3c618b3a561b250be6c950b8bd7/Memory.Contracts/IntuitiveCompilationDiagnostics.cs), sans texte
ou identifiant de source. Ils sont réservés au bilan de maintenance interne ;
ils ne changent aucun droit OAuth ni seuil de compilation. Les anciens rapports
restent lisibles avec un diagnostic absent (`null`), pas un succès supposé.
Voir les [unités, états et limites du bilan](https://github.com/gilldelia/J.E.S.S.I/blob/ab631e6d985aa3c618b3a561b250be6c950b8bd7/docs/qa/intuitive-diagnostics.md).
