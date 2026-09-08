> Copie publique d’un README de J.E.S.S.I. Seuls les README sont publiés ; les liens vers le code, les guides détaillés et les profils nécessitent l’accès au dépôt privé. Aucun de ces fichiers n’est exporté.

# Memory.Contracts

Ce projet est l'unique propriétaire des types publics échangés avec l'API
Memory. Il est partagé par le service, l'adaptateur MCP et l'ancien client
console afin que les mêmes noms, enums et formes JSON soient compilés partout.

Il ne doit contenir ni transport HTTP, ni accès Qdrant, ni fournisseur
d'embeddings, ni logique métier. Les contrats restent dans l'espace de noms
`Memory.DTOs` pour préserver la compatibilité source lors de cette extraction.
