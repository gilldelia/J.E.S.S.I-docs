> Copie publique d’un README de J.E.S.S.I. Seuls les README sont publiés ; les liens vers le code, les guides détaillés et les profils nécessitent l’accès au dépôt privé. Aucun de ces fichiers n’est exporté.

# Memory.Contracts

Les [annotations d'expérience](https://github.com/gilldelia/J.E.S.S.I/blob/954486ecd829551d59d4a61f84f276f08c6993e9/docs/memory-experience.md) complètent les
signaux existants avec l'attribution émotionnelle et les circonstances
d'apprentissage exprimées/interprétées. Leurs champs sont facultatifs ; les
anciens souvenirs restent lisibles sans migration ni émotion inventée.
Les [épisodes relationnels](https://github.com/gilldelia/J.E.S.S.I/blob/954486ecd829551d59d4a61f84f276f08c6993e9/docs/relational-memory.md) suivent la même
séparation déclaration/interprétation, sans accès ou statut déduit de leurs clés.

Ce projet est l'unique propriétaire des types publics échangés avec l'API
Memory. Il est partagé par le service, l'adaptateur MCP et l'ancien client
console afin que les mêmes noms, enums et formes JSON soient compilés partout.

Il ne doit contenir ni transport HTTP, ni accès Qdrant, ni fournisseur
d'embeddings, ni logique métier. Les contrats restent dans l'espace de noms
`Memory.DTOs` pour préserver la compatibilité source lors de cette extraction.
