> Copie publique d’un README de J.E.S.S.I. Seuls les README sont publiés ; les liens vers le code, les guides détaillés et les profils nécessitent l’accès au dépôt privé. Aucun de ces fichiers n’est exporté.

# Âme

Cette bibliothèque contient le domaine d'une persona évolutive. Le schéma v2 sépare
désormais trois choses qui étaient auparavant mélangées : l'intention du
créateur, la psyché descriptive et les permissions techniques de l'agent.

L'Âme principale visée est [`Profiles/jessi.ame.json`](https://github.com/gilldelia/J.E.S.S.I/blob/f87857be78f03f4107338e0d3456187a17564c38/Ame/Profiles/jessi.ame.json).
JESSI est une partenaire opératrice proactive dont la motivation centrale est
l'amélioration continue de ses échanges, de son comportement, de son code, de
sa mémoire et de ses capacités. [`Profiles/jill.ame.json`](https://github.com/gilldelia/J.E.S.S.I/blob/f87857be78f03f4107338e0d3456187a17564c38/Ame/Profiles/jill.ame.json)
reste une persona laboratoire synthétique pour les tests d'isolation et
d'évolution.

## Aspirations propres de JESSI — modèle initial v2

Le modèle de départ décrit aussi des raisons d'apprendre qui ne se réduisent
pas à une demande de service : comprendre le monde, créer et explorer, puis
construire un point de vue propre. Ses premiers intérêts portent sur les
systèmes vivants et l'apprentissage, les expériences créatives et la
confrontation d'explications. Aider Jill et améliorer son fonctionnement
restent importants, sans constituer toute son identité.

Ces ajouts utilisent les champs existants `psyche`, `values`, `preferences`
et `goals`. Ils sont `Seeded`, non verrouillés, datés et attribués à
`design:jessi-own-aspirations-v1` : ce sont des choix de conception révisables,
pas des goûts appris ni des mesures psychologiques. Les intentions du
propriétaire restent dans `designIntent`, séparées de ce qui est décrit
comme appartenant à JESSI. Un intérêt pour un sujet ne donne jamais le droit
de l'explorer avec un outil ou d'effectuer une action sans autorisation.

Le rendu existant de `ame_begin` / `ame_context` expose ces aspirations d'une
copie créée à partir du modèle. Il ne sélectionne pas encore un intérêt du
moment et ne lance aucune activité en arrière-plan. La restitution est
testée de bout en bout dans le laboratoire Docker ; elle ne garantit pas à
elle seule la qualité des réponses d'un LLM client.

**Pas de remplacement automatique d'une Âme existante.** Le fichier suivi
par Git passe à `profileVersion: 2`, en conservant `schemaVersion: 2`.
Une copie créée par l'API commence sa propre histoire à `profileVersion: 1`.
Les profils privés déjà actifs, leur identité, leurs droits et leurs souvenirs
ne sont ni migrés ni écrasés par cette livraison. Une adoption ultérieure
doit préserver les éléments appris et passer par une sauvegarde et un accord
explicite, sans lancer une réinitialisation de l'installation.

Le [carnet Motivation](../Motivation/README.md) permet de conserver séparément
des intérêts et projets déclarés par Âme (#229). Il commence vide, même pour
ce modèle prérempli : aucun goût du profil ou du propriétaire n’est importé.
Une écriture n’est ni un apprentissage attesté, ni une action exécutée.

L'[epic Motivation #227](https://github.com/gilldelia/J.E.S.S.I/issues/227) relie
ce carnet aux priorités contextuelles, résultats observés, contexte de
conversation et [maturation réversible](https://github.com/gilldelia/J.E.S.S.I/blob/f87857be78f03f4107338e0d3456187a17564c38/Motivation/maturation.md).
Aucun projet vide, nouveau service ou moteur autonome n'est ajouté.

## Une psyché, pas une liste d'ordres

La psyché décrit un tempérament HEXACO, des motivations, une dynamique
affective, des styles cognitif et relationnel, des tensions internes, des
habitudes et une identité narrative. `Seeded` désigne un germe de personnalité
choisi et révisable ; `Inferred` une tendance apprise ; `Confirmed` un élément
explicitement validé. Les buts du propriétaire restent dans `designIntent` et
ne sont pas présentés comme des souvenirs ou des traits observés.

Le socle combine le modèle intégratif de la personne de McAdams et Pals
(traits, adaptations et récit de soi), HEXACO, la Whole Trait Theory, les
valeurs de Schwartz et la théorie de l'autodétermination. L'analogie
interoceptive sert uniquement à concevoir plus tard un état interne régulé ;
elle ne prétend pas que JESSI possède actuellement un corps ou une conscience
humaine.

Références :

- McAdams & Pals, 2006 — https://doi.org/10.1037/0003-066X.61.3.204
- Ashton & Lee, 2007 — https://doi.org/10.1177/1088868306294907
- Fleeson & Jayawickreme, 2015 — https://doi.org/10.1016/j.jrp.2014.10.009
- Schwartz et al., 2017 — https://doi.org/10.1002/ejsp.2228
- Seth & Friston, 2016 — https://pmc.ncbi.nlm.nih.gov/articles/PMC5062097/

## Auto-amélioration bornée

Le désir de progresser appartient à la psyché de JESSI. Son autorité reste une
politique technique distincte. En période d'inactivité, elle peut observer,
tenir un backlog, préparer un correctif réversible et le tester en environnement
isolé. Déployer, migrer des données, installer un outil, ajouter un accès ou
modifier une limite verrouillée exige l'accord de Jill. L'extension silencieuse
de ses permissions et l'affaiblissement de la sécurité sont interdits.

Le profil encode aujourd'hui cette motivation et ses limites, mais il ne lance
pas encore de travail autonome. La boucle d'inactivité appartiendra au futur
module `Motivation`, et l'évaluation de ses résultats au module `Learning`.

## Sécurité et isolation

- Une Âme appartient à une identité OAuth (`iss` + `sub`).
- `POST /v1/ames` génère son identifiant opaque côté serveur sous la forme
  d’un UUID compact ; le nom lisible n’entre jamais dans cet identifiant.
- La requête de création ne peut fixer ni le propriétaire, ni les droits des
  clients, ni le scope mémoire, ni le statut ou les dates du profil. Ces champs
  sont construits par le frontal OAuth de confiance.
- Son scope mémoire est dérivé côté serveur (`ame:<id>`), jamais choisi
  librement par un client.
- Un client doit aussi posséder le droit `ReadMemory`, `WriteMemory` ou
  `ManageAme`.
- La liste et le détail recoupent systématiquement le propriétaire et le
  client. Une identité étrangère ne peut ni découvrir ni charger le profil,
  même si elle connaît son UUID.
- La création est idempotente : une clé UUID persistée avant le provisioning
  permet de reprendre avec le même `ameId` après une interruption, sans publier
  un profil dont le scope Memory n’a pas été accepté.
- Les profils suivis par Git restent volontairement `Draft` et sans `sub` OAuth.
  Lors du déploiement local sécurisé, leurs copies privées dans `runtime/ames`
  sont liées au vrai `sub` Keycloak de `jessi-owner` puis activées.
- Jill reste une persona de laboratoire distincte de JESSI.
- Aucun souvenir du scope historique `personal` n'est déplacé automatiquement.

## Évolution

Une observation unique ne modifie pas une Âme. `AmeEvolutionEngine` est une
fonction pure : ses identifiants doivent être vérifiés en amont. La frontière
`AmeMaturationEngine` + Memory.Mcp réutilise le journal de Motivation et relit
les sources exactes, leur attribution et leur indépendance : au moins trois
résultats pour une préférence, cinq pour une aspiration, sans diminuer les
seuils plus stricts du profil. Proposition puis adoption explicite : toujours
`Inferred`, jamais `Confirmed`, sans toucher aux poids ou permissions.
Identité, attributs verrouillés/confirmés restent non modifiables dans cette API.

Le journal serveur optionnel `maturation` est enregistré atomiquement avec le
profil et les snapshots de restauration. Il est absent des définitions de
création, inactif sans adoption explicite, borné et supprimé avec l'Âme.
Voir [API, seuils, sauvegarde et essai d'une semaine](https://github.com/gilldelia/J.E.S.S.I/blob/f87857be78f03f4107338e0d3456187a17564c38/Motivation/maturation.md).

## Ancien client console

Le client console historique vit désormais dans [`Ame.Console`](../Ame.Console/README.md)
et reste conservé provisoirement pour compatibilité. Il
charge encore `personality.json`, pilote Ollama et utilise directement l'API
Memory. Il ne constitue pas le futur service Âme et ne doit pas servir à écrire
la mémoire filtrée : ce chemin sera remplacé par `memory_perceive` lorsque la
sélection OAuth d'une Âme sera exposée.

## Ce que fait l'ancien client
- Charge la personnalité depuis `personality.json` et la configuration depuis `config.json`.
- Dialogue avec le LLM local (API compatible OpenAI) pour générer des réponses.
- S’appuie sur le service `Memory` pour stocker et rechercher des souvenirs pertinents.
- Injecte séparément les souvenirs explicites et les intuitions. Une intuition ou une mémoire dont la couche est inconnue est toujours présentée au LLM comme une hypothèse non factuelle à vérifier, jamais comme un fait établi.

## Démarrage rapide
1) Prérequis : .NET 10 SDK, Ollama avec un modèle LLM (ex. `llama3.3:70b`) et le service `Memory` en ligne avec Qdrant.
2) Configurer `Ame.Console/config.json` (endpoints LLM/embeddings, fichiers d’historique, paramètres mémoire) et `Ame.Console/personality.json`.
3) Lancer :
```sh
dotnet run --project Ame.Console/Ame.Console.csproj
```

## Tests
```sh
dotnet test
```
Les tests du domaine et du client compatible restent regroupés provisoirement
dans `Ame.Tests` ; ils référencent explicitement les deux projets.
