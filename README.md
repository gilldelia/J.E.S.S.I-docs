## Ame

# Ame

Client applicatif de Jessy (C#/.NET 10) qui pilote le LLM local via Ollama et consomme le microservice `Memory` pour la m�moire vectorielle.

## Ce que fait Ame
- Charge la personnalit� depuis `personality.json` et la configuration depuis `config.json`.
- Dialogue avec le LLM local (API compatible OpenAI) pour g�n�rer des r�ponses.
- S�appuie sur le service `Memory` pour stocker et rechercher des souvenirs pertinents.

## D�marrage rapide
1) Pr�requis : .NET 10 SDK, Ollama avec un mod�le LLM (ex. `llama3.3:70b`) et le service `Memory` en ligne avec Qdrant.
2) Configurer `config.json` (endpoints LLM/embeddings, fichiers d�historique, param�tres m�moire) et `personality.json`.
3) Lancer :
```sh
dotnet run --project Ame/Ame.csproj
```

## Tests
```sh
dotnet test
```
Tests du client dans `Ame.Tests`.




## Memory

# Memory

Microservice de m�moire vectorielle pour Jessy (API .NET 8), pens� pour se rapprocher d�un fonctionnement � m�moire humaine � : stockage durable, rappel contextuel, et pond�ration par r�currence/pertinence.

## Ce que fait le service
- Stocke des souvenirs texte dans Qdrant avec embeddings.
- Propose une recherche hybride (similarit� + r�currence).
- Expose des endpoints HTTP : insertion (`POST /memories/upsert`), recherche (`POST /memories/search`), sant� (`GET /healthz`).

## D�marrage rapide
1) Pr�requis : .NET 8 SDK, Qdrant, endpoint d'embeddings (ex. Ollama `nomic-embed-text`).
2) Configurer `appsettings.json` (section `Memory`) : URLs Qdrant et embeddings, collections, `TopK`, `ReorgMinutes`.
3) Lancer :
```sh
dotnet run --project Memory/Memory.csproj
```

## Docs & publication
- G�n�ration/push via `scripts/publish-docs.ps1` et workflow `.github/workflows/publish-docs.yml`.

## Tests
```sh
dotnet test
```
Tests du service dans `Memory.Tests`.




## Racine

# Jessy - Assistant numérique avec IA locale

Jessy est une solution .NET 10 qui combine un LLM local (Ollama) et une mémoire vectorielle (Qdrant) pour un assistant personnalisé.

## Projets
- `Ame/` : client applicatif et configuration.
- `Memory/` : microservice de mémoire vectorielle (API HTTP, Qdrant, embeddings).
- `scripts/` : outils CI/CD et publication de la documentation.

## Aperçu des capacités
- Personnalité configurable via `personality.json`.
- Mémoire court terme et long terme stockée dans Qdrant, avec rappel automatique des souvenirs pertinents.
- LLM local via API compatible OpenAI (modèle configurable, ex. `llama3.3:70b`).

## Démarrage rapide
1) Prérequis : .NET 10 SDK, Ollama avec un modèle LLM + `nomic-embed-text`, et Qdrant (port 6333).
2) Config minimale : ajuster `config.json` (LLM/embeddings) et `appsettings.json` du service Memory (endpoints Qdrant et embeddings).
3) Lancer l’application console :
```sh
dotnet run
```

## Tests
- Tests xUnit disponibles ; exécution :
```sh
dotnet test
```

## Documentation et publication
- Workflow GitHub Actions : `.github/workflows/publish-docs.yml`.
- Génération/push des docs : `scripts/publish-docs.ps1` (utilise `docs-out/`).

## Licence
Voir le fichier [LICENSE](LICENSE) pour les conditions complètes.




