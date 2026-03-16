# Racine

# J.E.S.S.I - Joint Environment for Self-evolving Synthetic Intelligence

J.E.S.S.I (Joint Environment for Self-evolving Synthetic Intelligence) est une solution .NET 10 qui combine un LLM local (Ollama) et une mémoire vectorielle (Qdrant) pour un assistant personnalisé.

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

## Déploiement Azure (Memory)

L'infrastructure est définie en Bicep dans `infra/`. Le pipeline CI/CD (`.github/workflows/deploy-memory.yml`) déploie automatiquement sur un push dans `main`.

### Prérequis Azure (à activer une seule fois)
1. Créer un abonnement Azure et un Resource Group.
2. Créer un Service Principal :
   ```sh
   az ad sp create-for-rbac --name "jessi-deploy" --role Contributor \
     --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> \
     --sdk-auth
   ```
3. Configurer les **secrets GitHub** du repo :

   | Secret | Description |
   |---|---|
   | `AZURE_CREDENTIALS` | JSON complet retourné par la commande ci-dessus |
   | `AZURE_RESOURCE_GROUP` | Nom du Resource Group |
   | `ACR_LOGIN_SERVER` | FQDN de l'ACR (disponible après le 1er déploiement Bicep) |
   | `ACR_USERNAME` | Nom d'utilisateur ACR |
   | `ACR_PASSWORD` | Mot de passe ACR |
   | `OPENAI_API_KEY` | Clé API Azure OpenAI (optionnel) |

4. Premier déploiement (provisionne l'infra) :
   ```sh
   az deployment group create \
     --resource-group <RESOURCE_GROUP> \
     --template-file infra/main.bicep \
     --parameters infra/main.bicepparam
   ```
5. Récupérer les valeurs ACR dans les outputs et les ajouter aux secrets GitHub.
6. Les prochains push sur `main` (modifiant `Memory/` ou `infra/`) déclenchent le déploiement automatique.

### Architecture déployée
- **Azure Container Apps** (Consumption) : Memory API + Qdrant.
- **Azure Files** : persistance Qdrant.
- **Azure Container Registry** (Basic) : images Docker.
- **Azure Key Vault** : secrets (clé API).
- **Log Analytics** : logs structurés.

## Licence
Voir le fichier [LICENSE](LICENSE) pour les conditions complètes.




## Ame

# Ame

Client applicatif de J.E.S.S.I (Joint Environment for Self-evolving Synthetic Intelligence) en C#/.NET 10 qui pilote le LLM local via Ollama et consomme le microservice `Memory` pour la mémoire vectorielle.

## Ce que fait Ame
- Charge la personnalité depuis `personality.json` et la configuration depuis `config.json`.
- Dialogue avec le LLM local (API compatible OpenAI) pour générer des réponses.
- S’appuie sur le service `Memory` pour stocker et rechercher des souvenirs pertinents.

## Démarrage rapide
1) Prérequis : .NET 10 SDK, Ollama avec un modèle LLM (ex. `llama3.3:70b`) et le service `Memory` en ligne avec Qdrant.
2) Configurer `config.json` (endpoints LLM/embeddings, fichiers d’historique, paramètres mémoire) et `personality.json`.
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

Microservice de mémoire vectorielle pour Jessy (API .NET 8), pensé pour se rapprocher d’un fonctionnement « mémoire humaine » : stockage durable, rappel contextuel, et pondération par récurrence/pertinence.

## Ce que fait le service
- Stocke des souvenirs texte dans Qdrant avec embeddings.
- Propose une recherche hybride (similarité + récurrence).
- Expose des endpoints HTTP : insertion (`POST /memories/upsert`), recherche (`POST /memories/search`), santé (`GET /healthz`).

## Démarrage rapide
1) Prérequis : .NET 8 SDK, Qdrant, endpoint d'embeddings (ex. Ollama `nomic-embed-text`).
2) Configurer `appsettings.json` (section `Memory`) : URLs Qdrant et embeddings, collections, `TopK`, `ReorgMinutes`.
3) Lancer :
```sh
dotnet run --project Memory/Memory.csproj
```

## Docs & publication
- Génération/push via `scripts/publish-docs.ps1` et workflow `.github/workflows/publish-docs.yml`.

## Tests
```sh
dotnet test
```
Tests du service dans `Memory.Tests`.




