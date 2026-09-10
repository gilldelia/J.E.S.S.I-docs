> Copie publique d’un README de J.E.S.S.I. Seuls les README sont publiés ; les liens vers le code, les guides détaillés et les profils nécessitent l’accès au dépôt privé. Aucun de ces fichiers n’est exporté.

# Memory

Microservice autonome de mémoire pour J.E.S.S.I, construit en .NET 10 et Qdrant.

Le modèle cognitif canonique est défini dans [INVARIANTS.md](https://github.com/gilldelia/J.E.S.S.I/blob/5e7eeb46beb376d73b1d6383701dc448c6c2e5f7/Memory/INVARIANTS.md). Memory n'est pas un simple RAG : il organise le cycle de vie d'une information entre contexte actif, souvenir explicite et connaissance internalisée.

## Modèle cible

### Mémoire court terme

Observations récentes, contexte actif et informations encore fragiles. Toute
observation admise entre d'abord dans cette couche ; un événement source
éphémère n'est pas encore un souvenir.

Configuration : `Memory:QdrantCollection` (nom historique conservé pour compatibilité).

### Mémoire long terme

Faits, expériences, décisions, préférences, contraintes, apprentissages et résumés consolidés avec leurs explications et leur provenance.

Configuration : `Memory:QdrantLongTermCollection`.

### Mémoire intuitive

Règles, réponses ou savoir-faire compilés à partir de plusieurs souvenirs long terme cohérents. Cette couche fournit une réponse rapide sans reconstruire tout le raisonnement, tout en conservant des liens internes vers ses preuves long terme lorsqu'une explication est demandée.

Configuration : `Memory:QdrantIntuitiveCollection`.

Une intuition passagère ou un signal faible n'est pas une mémoire intuitive.

## Cycle cognitif cible

Pendant la journée, le service ingère, rappelle et renforce les informations utiles.

Une fois par période de 24 heures, dans une fenêtre nocturne configurable (cible initiale : 03:00, `Europe/Paris`), le cycle de sommeil doit :

1. consolider ou oublier le court terme ;
2. réviser le long terme ;
3. compiler les connaissances intuitives ;
4. mettre à jour les signaux de vieillissement et d'utilité ;
5. produire un bilan explicable.

Le jalon M7 exécute actuellement la première étape automatiquement : consolidation ou oubli du court terme, avec bilan, limites et reprise idempotente. La compilation intuitive reste volontairement réservée à M8 et sera ajoutée après cette étape dans le même cycle nocturne.

Le rappel cible suit une cascade cognitive :

1. contexte court terme ;
2. réponse intuitive applicable ;
3. reconstruction long terme si nécessaire ou si une explication est demandée ;
4. arbitrage final sans confondre les couches.

## État actuel de l'implémentation

Déjà opérationnel :

- trois collections Qdrant distinctes ;
- insertion en court terme ;
- consolidation sélective vers le long terme ;
- oubli du court terme faible et expiré ;
- recherche sémantique et classement explicable ;
- renforcement configurable des résultats finaux ;
- métadonnées de couche et de type ;
- API protégée par `x-api-key` ;
- métriques OpenTelemetry ;
- déploiement local Docker Compose et collection Postman ;
- scénario end-to-end court terme → long terme ;
- contrat d'admission cognitif versionné `/v1` ;
- modalité sensorielle dérivée du canal réel (`Text`/`Voice` → audition, `Image`/`Video`/`Camera` → vue, capteurs spécialisés → sens correspondant) ;
- transport et persistance des décisions, préférences, contraintes, références explicites, importance et émotions avec provenance, confiance et preuves ; les émotions exprimées et inférées restent deux signaux distincts ;
- priorité des signaux explicites sur les inférences ;
- nouveauté calculée contre la mémoire du même scope ;
- scope personnel implicite et mode multi-scope explicitement filtré côté Qdrant ;
- registre privé et persistant des scopes d’Âmes dynamiques, borné et sans identité OAuth, activable par `Memory:DynamicAmeScopes` ;
- cycle nocturne automatique à heure locale et fuseau configurables, par défaut 03:00 `Europe/Paris` ;
- déclenchement manuel protégé, verrou anti-chevauchement, limites de durée/volume, bilan et métriques dédiées ;
- reprise idempotente : chaque source consolidée est supprimée séparément après l'écriture long terme durable, laquelle réutilise le même identifiant en cas de rejeu.

Écarts connus avec le modèle cible :

- Memory transporte les signaux inférés fournis par l'IA appelante, mais ne réalise pas lui-même l'analyse sémantique de la conversation ;
- le module optionnel `Perception`, décrit dans
  `docs/architecture/cognitive-target.md`, a commencé son noyau de domaine mais
  n'est pas encore relié au contrat d'admission Memory ;
- le CRUD OAuth des Âmes reste partiel : le frontal `Memory.Mcp` fournit la
  création et la consultation en ne transmettant ici qu’un `ameId` généré côté
  serveur, tandis que les opérations de modification et d’archivage restent à
  livrer par incréments séparés du jalon M12.

Ces écarts doivent être corrigés par incréments, dans l'ordre de la roadmap #53, sans migration destructive.

## API actuelle

- `POST /v1/memories/upsert` : admet une observation et calcule sa nouveauté dans son scope ;
- `POST /v1/memories/search` : rappelle en cascade dans un seul scope ;
- `PUT /v1/internal/ames/{ameId}/memory-scope` : enregistre de façon idempotente le scope privé d’une Âme créée par le frontal OAuth ;
- `GET /v1/maintenance/sleep-cycle` : expose l'état, le dernier bilan et la prochaine exécution automatique ;
- `POST /v1/maintenance/sleep-cycle` : déclenche manuellement le cycle pour l'exploitation et les tests ;
- `GET /healthz` : vérifie la santé du service.

Le cycle est configuré sous `Memory:SleepCycle` avec `TimeZoneId`, `LocalStartTime`, `MaximumDurationMinutes` et `MaximumItemsPerScope`. Il n'existe plus de boucle de réorganisation globale toutes les quelques minutes.

Lorsque `Memory:DynamicAmeScopes:Enabled` est actif, l’opération interne
d’enregistrement accepte uniquement un GUID d’Âme non vide, normalise son scope
en `ame:<guid sans tirets>` et l’ajoute au registre local. Les scopes configurés
historiques restent inchangés. Le cycle de sommeil traite l’union des scopes
configurés et enregistrés. Ce registre ne contient aucun `iss`, `sub`, nom ou
jeton OAuth et son endpoint ne doit jamais être exposé directement sur Internet.

Le contrat d'admission reçoit un `channel` réel, une origine et éventuellement deux groupes de signaux : `explicitSignals` et `inferredSignals`. L'explicite gagne champ par champ. Le champ `sense` est produit par Memory à partir du canal et n'est pas choisi d'après le texte. Plusieurs perceptions d'une même entrée multimodale peuvent partager un `perceptionGroupId`.

Un adaptateur de confiance peut aussi fournir un `admissionId` GUID stable. Le
premier appel crée l'observation ; un rejeu strictement identique retourne
l'observation existante sans recalcul d'embedding ni seconde écriture. Réutiliser
le même identifiant pour un contenu, un canal, une origine ou un groupe différent
produit un conflit explicite. Les admissions simultanées partageant le même
couple scope/identifiant sont sérialisées, tandis que les autres restent
indépendantes. Memory fonctionne pour l'instant avec un écrivain unique ; la
configuration Azure est limitée à un réplica tant qu'un verrou atomique partagé
n'a pas remplacé cette garantie locale.

Chaque résultat de recherche expose :

- `layer` : `ShortTerm`, `LongTerm` ou `Intuitive` ;
- `type` : observation, fait, résumé, intuition ou contenu historique inconnu ;
- `recallScore` : score final normalisé dans `[0,1]` ;
- `recallSignals` : détail des signaux et de leur contribution.

La réponse conserve `results` pour compatibilité et ajoute `activeContext`, `evidence` et
`decision`. `decision.path` et `decision.reasons` décrivent l'arbitrage typé. Le court terme
établit d'abord `activeContext`; une intuition active, suffisamment fiable et dont toutes les
conditions sont comprises et vérifiées peut répondre ensuite. Sinon, le service recherche le long
terme. Fournir `explainIntuitiveMemoryId` (un GUID) étend uniquement les identifiants
`sourceMemoryIds` de cette intuition, dans le même scope, dans `evidence`; il ne déduit pas une
intuition à expliquer à partir d'un « pourquoi ? » sans identifiant.

Les réglages de rappel sont sous `Memory:Recall` : `IntuitiveMinimumConfidence` (0.8),
`CandidateMultiplier` (3) et `MaximumExplanationEvidenceIds` (50). La demi-vie intuitive
par défaut est de 90 jours et ne peut pas être inférieure à celle du long terme.

Le service crée les collections manquantes mais ne recrée jamais automatiquement une collection dont la dimension vectorielle est incompatible.

## Démarrage local recommandé

Prérequis : Docker Engine ou Docker Desktop avec Docker Compose v2.

Depuis la racine du dépôt :

```bash
bash ./scripts/local/memory-local.sh up
```

Endpoints locaux :

- Memory : `http://localhost:8080` ;
- Swagger : `http://localhost:8080/swagger` ;
- Qdrant : `http://localhost:6333`.

La clé locale par défaut est `local-memory-key`. Elle ne doit jamais être réutilisée hors de l'environnement local.
En dehors des environnements `Development` et `Testing`, `Memory:IncomingApiKey`
doit contenir une clé non vide : Memory refuse sinon de démarrer. Le déploiement
Terraform impose la même règle pour `staging` et `prod`.

Importer dans Postman :

- `postman/JESSI.Memory.Local.postman_collection.json` ;
- `postman/JESSI.Memory.Local.postman_environment.json`.

Vérification automatisée complète :

```bash
bash ./scripts/local/memory-local.sh verify
```

## Exécution directe pour le développement

Prérequis : .NET 10 SDK, Qdrant et un endpoint d'embeddings compatible OpenAI.

Configurer la section `Memory` dans `appsettings.json`, puis lancer :

```bash
dotnet run --project Memory/Memory.csproj
```

## Tests

```bash
dotnet test
```

Le scénario `MemoryLifecycleEndToEndTests` traverse les endpoints, le calcul de nouveauté, l'isolation des scopes, le rappel, la consolidation et l'écriture long terme avec des frontières externes déterministes. Il ne nécessite ni Azure, ni Qdrant, ni LLM local.

## Documentation API

La documentation OpenAPI est exposée par Swagger. La [publication publique](https://github.com/gilldelia/J.E.S.S.I/blob/5e7eeb46beb376d73b1d6383701dc448c6c2e5f7/docs/readme-publication.md)
recopie uniquement les README sélectionnés depuis `main` ; aucun export Swagger,
profil ou fichier de configuration n'est publié. Le script historique
`scripts/publish-docs.ps1` est retiré et ne doit plus être utilisé.
