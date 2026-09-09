> Copie publique d’un README de J.E.S.S.I. Seuls les README sont publiés ; les liens vers le code, les guides détaillés et les profils nécessitent l’accès au dépôt privé. Aucun de ces fichiers n’est exporté.

# J.E.S.S.I — une mémoire et une identité qui évoluent

**Joint Environment for Self-evolving Synthetic Intelligence**

J.E.S.S.I explore une IA personnelle qui garde une continuité entre les échanges :
des souvenirs sélectionnés, une identité distincte et des intérêts construits
à partir d'expériences. L'objectif n'est pas de tout archiver, mais de pouvoir
expliquer ce qui a été retenu, pourquoi cela compte et ce qui a changé.

Le socle actuel est une solution **.NET 10**, auto-hébergeable avec **Docker Linux**,
**Qdrant** pour la mémoire et **Ollama** pour les modèles locaux. Il s'utilise
par API HTTP ou avec un client **MCP**. C'est un projet expérimental : une
mémoire organisée et un profil descriptif ne prouvent ni conscience, ni émotions
ressenties, ni reproduction fidèle d'une personne.

## Ce qui fonctionne aujourd'hui

- **Une Âme, une mémoire.** Une Âme est un profil d'identité, de valeurs et de
  tendances. Avec le routage OAuth activé, chaque utilisateur gère ses Âmes et
  seuls ses clients autorisés peuvent y accéder. Le propriétaire vient du
  jeton ; chaque appel mémoire désigne l'Âme, jamais un espace de stockage libre.
- **Une mémoire sélective à trois couches.** Les observations admises entrent
  en court terme. La consolidation peut en conserver une partie en long terme ;
  des expériences suffisamment étayées peuvent produire des règles intuitives.
  Le rappel garde la provenance, la couche et les raisons de sa décision.
- **Une perception qui tient compte des acquis.** En mode adaptatif avec le
  classifieur local, une découverte, une confirmation encore fragile ou une
  nuance peut être retenue ; une répétition sans apport peut rester éphémère.
  La comparaison a lieu avant la décision, dans la mémoire autorisée.
  Voir les [règles et limites de cette comparaison](https://github.com/gilldelia/J.E.S.S.I/blob/1b7d98908454776c1334ab655e58373949b6816c/docs/contextual-perception.md).
- **Des intérêts issus de traces, pas des goûts copiés.** Le carnet de chaque
  Âme commence vide. Des appels explicites peuvent y ajouter des projets ou
  traiter des souvenirs sourcés pour faire émerger et réviser des pistes.
  Les goûts du propriétaire restent distincts de ceux attribués à l'Âme.
- **Une évolution explicable et réversible du profil.** La maturation propose
  des préférences ou aspirations ; leur adoption reste explicite, sourcée et
  réversible. Une proposition ou une inférence n'est pas un goût confirmé.
- **Un contexte conversationnel sourcé.** Le client reçoit les traits et les
  pistes actuelles avec de courts extraits, leur attribution et leurs limites.
  Une source supprimée ou modifiée ne soutient plus cette explication à la
  lecture suivante. Lire le contexte ne lance aucun apprentissage ni projet.

## Du message au souvenir

1. Le client choisit une Âme autorisée pour la conversation.
2. Il transmet une observation à Perception, qui décide de l'admettre ou de
   la laisser éphémère. Une mémorisation volontaire reste possible séparément.
3. Memory conserve l'observation admise en court terme. Son cycle de
   consolidation peut ensuite la conserver, l'oublier ou alimenter le long terme.
4. Le rappel retrouve le contexte actif, une règle intuitive applicable ou les
   souvenirs long terme nécessaires, sans confondre leurs rôles.
5. Le client utilise ces données pour répondre. L'apprentissage des intérêts
   et l'adoption d'un trait nécessitent leurs propres appels explicites.

Ce parcours ne réentraîne pas les poids du LLM. Ici, « apprendre » désigne
l'évolution des souvenirs, des traces documentées et, après adoption, du profil.
Les [invariants mémoire](https://github.com/gilldelia/J.E.S.S.I/blob/1b7d98908454776c1334ab655e58373949b6816c/Memory/INVARIANTS.md) décrivent les règles du cycle.

## Les briques du projet

| Module | Rôle actuel |
|---|---|
| [Memory](Memory/README.md) | Stockage, admission, rappel, consolidation, règles intuitives et oubli. |
| [Memory.Contracts](Memory.Contracts/README.md) | Contrats HTTP partagés, sans stockage ni logique métier. |
| [Perception](Perception/README.md) | Filtre explicable avant mémorisation ; ne stocke rien lui-même. |
| [Ame](Ame/README.md) | Profils, valeurs, tendances et évolution explicite ; droits techniques séparés. |
| [Motivation](Motivation/README.md) | Carnet persistant, choix contextuel et journal d'expériences par Âme. |
| [Memory.Mcp](Memory.Mcp/README.md) | Entrées HTTP/MCP, contrôle OAuth et coordination des modules. |
| [Ame.Console](Ame.Console/README.md) | Ancien client de compatibilité, pas le point d'entrée du parcours OAuth par Âme. |

Pour contribuer, commencer par la [carte du projet](https://github.com/gilldelia/J.E.S.S.I/blob/1b7d98908454776c1334ab655e58373949b6816c/docs/project-map.md) :
elle indique les points d'entrée, contrats, invariants et tests à lire selon
l'évolution, sans parcourir tout le dépôt. L'[architecture cible](https://github.com/gilldelia/J.E.S.S.I/blob/1b7d98908454776c1334ab655e58373949b6816c/docs/architecture/cognitive-target.md)
distingue les modules existants des travaux futurs.

## Essayer en local

L'accès au dépôt source est nécessaire. Sur Windows, utiliser **PowerShell 7**
et **Docker Desktop en mode conteneurs Linux** ; ne pas lancer les projets
.NET, leurs DLL ou leurs tests directement sur l'hôte.

Le [guide local](https://github.com/gilldelia/J.E.S.S.I/blob/1b7d98908454776c1334ab655e58373949b6816c/docs/runbooks/local-memory-real.md) détaille les modèles,
le support GPU NVIDIA du profil fourni et le stockage persistant. Prévoir les
téléchargements des images et modèles au premier démarrage. Le profil local
à jeton statique sert au diagnostic ; pour tester les comptes et l'isolation
par Âme, suivre le [guide OAuth auto-hébergé](https://github.com/gilldelia/J.E.S.S.I/blob/1b7d98908454776c1334ab655e58373949b6816c/docs/runbooks/home-oauth.md).

### Préparer les services

Le profil OAuth fourni est lié aux domaines de l'installation d'origine :
**ce n'est pas un démarrage OAuth autonome limité à localhost**. Pour un
nouveau poste, adapter d'abord l'ensemble de la configuration d'identité à
sa propre installation ; changer seulement l'adresse du navigateur ne suffit pas.
Pour une installation dont ces paramètres ont été préparés, lancer depuis la
racine du dépôt :

```powershell
pwsh -File .\scripts\local\memory-real.ps1 init
pwsh -File .\scripts\local\memory-real.ps1 secure-up
pwsh -File .\scripts\local\memory-real.ps1 secure-verify
```

Ces commandes initialisent les secrets privés et les profils de départ, puis
démarrent les services locaux, **sans démarrer la passerelle HTTPS Caddy ni
ouvrir l'accès Internet**. `secure-verify` contrôle les métadonnées, Swagger
et les refus sans authentification ; il ne teste pas la connexion complète
dans un navigateur et ne remplace pas la certification ci-dessous.

### Rendre la connexion OAuth utilisable

Swagger peut s'afficher sur localhost alors que sa connexion ne fonctionne
pas : ses URL de connexion et d'échange de jetons utilisent l'autorité OAuth
HTTPS configurée, pas le port local de Keycloak. Avant de se connecter,
cette autorité doit être accessible, avec un certificat valide et des URL
de retour cohérentes, et appartenir à **sa propre installation**.
Le guide distingue la préparation DNS/réseau du
[démarrage de la passerelle HTTPS](https://github.com/gilldelia/J.E.S.S.I/blob/1b7d98908454776c1334ab655e58373949b6816c/docs/runbooks/home-oauth.md#démarrer-https).
Les domaines préconfigurés ne sont pas un service fourni aux contributeurs.
Ne pas désactiver TLS ou les contrôles d'identité pour franchir cette étape.

Le Swagger des Âmes et de Perception se trouve sur
`http://127.0.0.1:8082/swagger` ; celui de Memory sur
`http://127.0.0.1:8080/swagger` décrit le service de stockage interne.
**Une fois le prérequis HTTPS ci-dessus satisfait**, utiliser le premier pour
le parcours utilisateur OAuth :

- se connecter, puis lister les Âmes avec `GET /v1/ames` ;
- choisir l'`ameId` retourné pour les routes `/v1/ames/{ameId}/...` ;
- consulter les [contrats API/MCP](Memory.Mcp/README.md) et le
  [protocole d'essai sur une semaine](https://github.com/gilldelia/J.E.S.S.I/blob/1b7d98908454776c1334ab655e58373949b6816c/Motivation/maturation.md#essai-utilisateur-sur-une-semaine).

Les scénarios automatisés qui créent des données doivent toujours viser le
laboratoire jetable, jamais les souvenirs personnels. Ne jamais publier les
fichiers d'environnement, profils actifs, jetons ou sauvegardes.

## Avec un client MCP ou ChatGPT

L'interface MCP est exposée sur `/mcp`. `ame_begin` démarre la sélection
pour une conversation ; les appels suivants conservent le même `ameId` et
le même `sessionId`. `ame_context` recharge le profil descriptif et ses
pistes ; `memory_perceive` et `memory_recall` alimentent et consultent la mémoire.

**Connexion ne signifie pas écoute globale.** J.E.S.S.I ne reçoit que les
appels effectivement envoyés par le client ; elle ne lit pas automatiquement
toutes les conversations ChatGPT. Le client doit charger le contexte et
appeler les outils appropriés. La configuration et les essais de sélection
d'outils côté ChatGPT sont décrits dans la
[documentation officielle OpenAI](https://developers.openai.com/plugins/deploy/connect-chatgpt).

## Validation et contributions

La CI et la QA s'exécutent chez l'opérateur, dans une **VM Linux jetable avec
Docker**, indépendamment de GitHub Actions. Un cycle complet vérifie le SHA
exact : build, cinq suites .NET, tests des outils, dépendances, API/MCP, OAuth,
séparation entre utilisateurs et Âmes, redémarrage réel et nettoyage.
Voir la [CI locale](https://github.com/gilldelia/J.E.S.S.I/blob/1b7d98908454776c1334ab655e58373949b6816c/docs/local-ci.md) et la [certification M12](https://github.com/gilldelia/J.E.S.S.I/blob/1b7d98908454776c1334ab655e58373949b6816c/docs/qa/m12-e2e.md).

Le lanceur préautorisé permet les cycles ordinaires sans nouvelle UAC après
son installation administrative. Une mise à jour de sa partie privilégiée
reste une intervention distincte et contrôlée.

Pour proposer une contribution :

1. Définir un résultat limité et ses critères d'acceptation dans une issue.
2. Lire les [règles du dépôt](https://github.com/gilldelia/J.E.S.S.I/blob/1b7d98908454776c1334ab655e58373949b6816c/AGENTS.md), puis le parcours de la carte concerné.
3. Développer sur une branche dédiée depuis `dev`, avec tests et documentation.
   **Une fonctionnalité ou un bug = une PR**, y compris pour les outils et la doc.
4. Suivre la [revue indépendante et les promotions](https://github.com/gilldelia/J.E.S.S.I/blob/1b7d98908454776c1334ab655e58373949b6816c/docs/development-workflow.md) :
   PR vers `dev`, CI post-fusion, promotion `stage`, QA du SHA exact,
   puis PR vers `main`. Un seul rôle travaille à la fois ; un bug bloquant
   passe avant les fonctionnalités.

Ne jamais mettre de souvenirs réels ni de secrets dans les fixtures ou les
rapports partagés. Une promotion Git ne met pas à jour une installation
personnelle : le déploiement reste une opération séparée.

## Limites et suite

Le transport et l'isolation peuvent être testés sans certifier la qualité de
chaque phrase produite par un LLM. Les essais conversationnels ont encore
montré des interprétations infidèles et des réponses hors sujet : la fidélité
du rendu reste à améliorer. La recherche est bornée et les scores de
perception ou de motivation sont des choix logiciels, pas des mesures biologiques.

La boucle autonome, le travail pendant l'inactivité, l'auto-modification du
code et un module Learning dédié restent des objectifs, **pas des capacités
actives**. Aucun intérêt du carnet ne donne une permission d'agir. Le cycle
de maintenance de Memory est distinct d'un moteur autonome de personnalité.
Azure et l'extension de l'observabilité restent en pause.

## Documentation publique

[J.E.S.S.I-docs](https://github.com/gilldelia/J.E.S.S.I-docs) présente le projet
sans donner accès au dépôt source privé. Les liens vers du code, des contrats
ou des guides internes nécessitent cet accès.

La seule exception GitHub Actions autorisée est la [publication des README](https://github.com/gilldelia/J.E.S.S.I/blob/1b7d98908454776c1334ab655e58373949b6816c/docs/readme-publication.md)
sélectionnés depuis `main`. Elle conserve les autres fichiers du dépôt public
et ne copie ni configuration, ni profil, ni guide interne. Le secret d'accès
au seul dépôt documentaire est nécessaire ; la CI et la QA restent locales.
L'ancien `scripts/publish-docs.ps1` est retiré et n'effectue plus aucune copie.

## Licence

Voir le fichier [LICENSE](https://github.com/gilldelia/J.E.S.S.I/blob/1b7d98908454776c1334ab655e58373949b6816c/LICENSE) du dépôt source.
