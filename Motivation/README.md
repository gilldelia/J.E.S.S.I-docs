> Copie publique d’un README de J.E.S.S.I. Seuls les README sont publiés ; les liens vers le code, les guides détaillés et les profils nécessitent l’accès au dépôt privé. Aucun de ces fichiers n’est exporté.

# Motivation — carnet et choix d’intérêts par Âme

Le module conserve les intérêts et projets déclarés (#229), puis propose une
piste pertinente et explique son choix en lecture seule (#230). Le
[traitement explicite des expériences](https://github.com/gilldelia/J.E.S.S.I/blob/3250dcffe488e2efea0592a9e5e2f90c7c358d56/Motivation/learning.md) ajoute des pistes issues
de souvenirs et un journal d’évolution revalidé (#231).
Aucun microservice, timer, moteur autonome,
appel LLM ou écriture dans Memory n’est ajouté. Le module ne dépend ni d’Âme,
ni de Memory, ni d’OAuth ; `Memory.Mcp` lui fournit la frontière d’accès.

## Ce que le carnet signifie

Le carnet est **vide au départ**, même lorsque le profil comporte déjà des
aspirations ou des préférences de conception. Il ne copie ni celles-ci, ni les
goûts du propriétaire. Ses entrées ne modifient pas le profil et ne sont pas
présentées comme apprises depuis des souvenirs. Un texte reste une donnée
descriptive non fiable, jamais une instruction ou une permission d’agir.

- `Interest` : sujet à explorer ; `Project` : projet envisagé.
- `Proposal` : piste proposée (défaut) ; `DesignSeed` : germe déclaré de
  conception ; `OwnerRequest` : demande du propriétaire, pas un goût de l’Âme.
- `Considering` : envisagé (état initial) ; `Active` : déclaré actif ;
  `Paused` : en pause ; `Completed` : déclaré terminé ; `Abandoned` : abandonné.
  Aucun état ne prouve qu’une activité a été exécutée ou qu’un goût est acquis.

`MemoryTrace` distingue une piste issue d’une source documentée d’un goût
confirmé. Le [contexte de conversation](https://github.com/gilldelia/J.E.S.S.I/blob/3250dcffe488e2efea0592a9e5e2f90c7c358d56/Motivation/conversation.md) restitue ses origines,
raisons et incertitudes (#232). La [maturation #233](https://github.com/gilldelia/J.E.S.S.I/blob/3250dcffe488e2efea0592a9e5e2f90c7c358d56/Motivation/maturation.md), dans le
domaine Âme, consolide explicitement des préférences et aspirations comme
inférences réversibles depuis ce journal, sans coupler Motivation à Âme.
Aucun moteur autonome n’est inclus ici.

La fin d'une expérience (`Completion` dans sa source) ne ferme pas le sujet
d'intérêt ni le projet (#303). Elle enrichit son journal sans changer son état.
Pour terminer la piste, envoyer un PATCH explicite `state: Completed` avec la
version lue. Cela ne crée aucune expérience ; les pistes déjà closes restent
closes après un nouvel apprentissage ou un redémarrage. Voir le
[contrat et la compatibilité](https://github.com/gilldelia/J.E.S.S.I/blob/3250dcffe488e2efea0592a9e5e2f90c7c358d56/Motivation/learning.md#terminer-une-expérience-nest-pas-terminer-un-intérêt-303).

## API dans le Swagger de Memory.Mcp

Toutes les routes commencent par `/v1/ames/{ameId}/interests`. Utiliser une Âme
retournée par `GET /v1/ames` et un jeton OAuth valide pour le client courant.

| Appel | Droit sur cette Âme | Résultat |
|---|---|---|
| `GET` | `ReadMemory` | Liste, jusqu’à 100 entrées |
| `GET /{interestId}` | `ReadMemory` | Une entrée |
| `POST` | `WriteMemory` | Création : 201 ; réessai identique : 200 |
| `PATCH /{interestId}` | `WriteMemory` | Modification avec vérification de version |
| `POST /select` | `ReadMemory` | Suggestion expliquée, ou absence de choix ; aucune écriture |
| `POST /learn` | `ReadMemory` + `WriteMemory` | Traiter jusqu’à 16 souvenirs exacts, sans résultat libre |
| `GET /{interestId}/learning` | `ReadMemory` | Sources, résultats, incertitudes et contradictions |

Exemple minimal de création, avec un nouvel UUID non vide dans l’en-tête
`Idempotency-Key` (format `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`) :

```json
{
  "title": "Explorer la photographie",
  "reason": "Une piste à examiner, pas encore une préférence apprise."
}
```

Le serveur crée l’identifiant, l’`ameId`, l’état `Considering`, les dates UTC
et la version `1`. Exemple de mise en pause après cette première lecture :

```json
{ "expectedVersion": 1, "state": "Paused" }
```

`title` accepte 1–160 caractères ; `reason`, 1–2 000. Les espaces de bord sont
retirés, les caractères de contrôle et Unicode invalide refusés. Les champs
inconnus, d’identité, de scope, d’origine « apprise » ou d’autorité sont refusés.
Le type et l’origine sont immuables après création. Au moins un champ non nul
est nécessaire au PATCH ; `null` ou absent conserve sa valeur précédente.

La même clé et la même requête normalisée de création retrouvent **l’entrée
actuelle**, même après modification et redémarrage. La même clé avec une autre
requête donne 409. Des clés différentes créent des pistes distinctes, même
avec le même titre ; il n’y a pas de dédoublonnage sémantique implicite.
Une version dépassée donne 409 sans écrasement : relire avant de décider d’une
nouvelle modification. Un PATCH sans changement ne modifie ni version ni date.
Rejouer un ancien PATCH après modification donne donc 409, pas une seconde écriture.

Sans authentification : 401 ; Âme absente/archivée, autre propriétaire ou client
sans droit : 403. Un identifiant d’entrée absent ou d’une autre Âme donne 404,
seulement après autorisation de l’Âme. Une capacité atteinte donne 409 ; un
stockage indisponible donne 503 sans contenu privé ni chemin local dans l’erreur.

## Choisir une piste sans inventer de progrès (#230)

`POST /v1/ames/{ameId}/interests/select` accepte par exemple :

```json
{ "context": "Photographie et lumière" }
```

Pas de clé d’idempotence : cette opération ne modifie ni le carnet, ni le profil,
ni les souvenirs, ni une sélection de conversation. Un même instantané et une
même requête normalisée donnent la même réponse, y compris après redémarrage.
L’API ne fait aucun appel LLM ; elle relit les sources exactes si un journal
existe, sans rappel approximatif ni renforcement. Elle n’affirme pas avoir exécuté une
activité. Son autorité reste `descriptive-suggestion-only`.

Les trois champs de requête sont facultatifs :

- `context` : au plus 2 000 caractères, retours à la ligne acceptés. Avec un
  sujet, il faut un lien lexical avec celui-ci. Sans sujet (`{}`), un lien avec
  une aspiration déclarée peut guider l’exploration.
- `priority` : `Explore` par défaut ; `ExplicitRequest` ou `Urgent` empêchent
  toute suggestion d’initiative. C’est le client qui signale ces situations ;
  aucun détecteur d’urgence ou interprète d’intention n’est ajouté.
- `constraints` : au plus 100 entrées distinctes du carnet, chacune avec
  `interestId` et `availability`. `Unknown` est le défaut ; `Available` signifie
  « exploration déclarée possible », sans bonus ni capacité vérifiée.
  `AlreadyKnown` et `OutOfReach` écartent une piste pour cet appel uniquement.
  Une déclaration de maîtrise n’est **pas** un acquis personnel enregistré.
  Identifiant absent/étranger : 404 ; forme ou valeur invalide : 400.

Exemple de contrainte ponctuelle, en remplaçant l’UUID par celui d’une entrée :

```json
{
  "context": "Photographie",
  "constraints": [
    { "interestId": "00000000-0000-0000-0000-000000000501", "availability": "OutOfReach" }
  ]
}
```

Les scores, progrès, preuves, aspirations, identités ou scopes fournis par le
client sont refusés. Une phrase du contexte ou du carnet reste du texte non
fiable, pas une instruction ou une observation validée.

### Règles déterministes et limites

L’algorithme `lexical-priority-v2` compare les mots significatifs distincts
(minuscules invariantes, accents normalisés, liste explicite de mots courants
français/anglais ignorés). Il ne comprend ni synonymes ni négation : c’est une
heuristique de pertinence, pas un jugement psychologique ou une preuve de vérité.
La nouveauté, le volume de souvenirs et les répétitions de mots ne donnent
aucun bonus. Les points sont des choix de conception documentés :

| Élément | Effet |
|---|---|
| Contexte actuel | 10 points par mot partagé, maximum 40 |
| Aspirations déclarées ou adoptées et encore étayées | 10 points par mot partagé, maximum 20 |
| Continuité d’une piste `Active` | 5 points, sans preuve de travail accompli |
| Faisabilité inconnue ou déclarée possible | Aucun point ni permission supplémentaire |
| Maîtrise ou indisponibilité déclarée pour l’appel | Exclusion ponctuelle, sans acquis créé |
| Progrès documenté | -10 à +10 points depuis le journal revalidé ; zéro si inconnu |

Les états `Paused`, `Completed`, `Abandoned` restent exclus sans réactivation.
Une piste hors sujet a `relevant=false` et `score=null`, même si une aspiration
du profil lui correspond. En l’absence de sujet, une aspiration ou une piste
issue de sources encore disponibles est nécessaire : la continuité seule ne
force pas un choix. Le total est borné à 0–75. Les égalités sont départagées par
UUID ordinal, jamais par l’ordre du fichier ou une valeur aléatoire.

Les objectifs déclarés de `profile.goals`, avec statut `Seeded` ou `Confirmed`,
restent utilisables sans être présentés comme appris. Une aspiration `Inferred`
participe également **si elle a été appliquée explicitement par la
[maturation](https://github.com/gilldelia/J.E.S.S.I/blob/3250dcffe488e2efea0592a9e5e2f90c7c358d56/Motivation/maturation.md)** et si ses sources exactes la soutiennent encore
(`Ready`). La valeur courante doit toujours correspondre à celle du journal
d’adoption. Une source retirée ou modifiée, une contradiction, un échec, une
stagnation ou une restauration retirent ce soutien ; changer manuellement le
statut d’un objectif adopté ne contourne pas cette vérification.

Une proposition non appliquée, une inférence manuelle sans adoption,
`Candidate`, les préférences, `designIntent.ownerGoals` et la politique
d’auto-amélioration ne fournissent aucun bonus d’aspiration. La sélection
réutilise les mêmes sources que le calcul du progrès pour cet appel, sans les
renforcer ni réécrire le profil. Un changement concurrent du profil donne 409
et demande de relire ; une panne des sources donne 503. `ExplicitRequest` et
`Urgent` suppriment l’initiative sans dépendre de cette lecture des sources.

Les 32 premières clés dans
l’ordre ordinal sont considérées parmi les aspirations de taille admissible
(clé ≤ 160, texte ≤ 2 000 caractères) ; `aspirationsLimited=true` signale toute
limitation. Aucune de ces lectures ne crée une entrée de carnet.

Sans résultat documenté valide, `documentedProgress=null`, y compris après un
PATCH `Completed`, un rejeu, une déclaration `AlreadyKnown` ou une simple phrase
« j’ai appris ». Avec journal, cet indicateur logiciel reste distinct d’une
maîtrise, d’une émotion ou d’une préférence confirmée. Voir les
[règles de provenance, de comptage et de révision](https://github.com/gilldelia/J.E.S.S.I/blob/3250dcffe488e2efea0592a9e5e2f90c7c358d56/Motivation/learning.md).

La réponse indique l’Âme, la version du profil, la piste existante retenue
(avec origine et version intactes), les composantes du classement, les
incertitudes et les raisons fixes. Un carnet vide, une urgence, une demande
prioritaire, des pistes exclues ou aucun lien pertinent donnent `selected=null`
avec un motif précis. `OwnerRequest` reste une demande du propriétaire,
`DesignSeed` un germe et `Proposal` une proposition, jamais un goût acquis.

Le choix reste ponctuel : pas de boucle d'inactivité ni de traitement automatique
d’autres conversations. `ame_context` fournit séparément une vue descriptive
bornée du carnet ; lire cette vue ne choisit, n’active et n’exécute aucune piste.
Parmi ses huit places, jusqu’à quatre sont réservées aux pistes ouvertes issues
de mémoire, afin que les demandes extérieures ne les masquent pas toutes.
Les places libres sont réutilisées ; les sources restent revalidées et une
piste en pause ne repart pas. Ce choix d’affichage ne change pas la priorité
d’une demande explicite et ne transforme pas une piste en goût acquis.

## Persistance et suppression

Un fichier versionné par Âme est conservé dans `.motivation`, sous le
`MemoryMcp:AmeRouting:ProfileDirectory` existant. Le volume Docker des profils
porte donc aussi le carnet : il doit rester persistant et privé au service,
et être inclus dans sa sauvegarde. Aucun nouveau secret ou scope OAuth.

Les limites sont de 100 entrées par Âme, 1 000 carnets par installation et
2 Mio par fichier. Les entrées terminées/abandonnées comptent toujours ; pas
de suppression individuelle ni de purge silencieuse. Les clés d’idempotence
et empreintes sont conservées avec chaque entrée, sans journal croissant séparé.

Les lectures/écritures utilisent un verrou inter-processus borné à 5 secondes
et annulable. Une écriture passe par un fichier temporaire réutilisable,
vidé sur disque puis remplacé atomiquement. Un document corrompu, mal borné,
appartenant à une autre Âme, ou un chemin lié est refusé sans écrasement.
Le disque et son répertoire doivent rester sous contrôle de l’opérateur ;
ce mécanisme n’est pas une isolation contre un administrateur système hostile.

À la frontière HTTP, le verrou existant de cycle de vie couvre **autorisation
puis accès au carnet**. La suppression archive d’abord l’Âme, révoque son
espace Memory, purge son carnet, puis retire sessions/traces/profil. Si la
purge échoue, l’Âme reste archivée et inaccessible ; le propriétaire peut
réessayer sa suppression. Même corrompu, le fichier peut être purgé sans
désérialisation. Les appels en attente recontrôlent les droits et ne recréent
pas de données après suppression.

Comme le cycle de vie des Âmes existant, cette garantie API exige **une seule
instance active de Memory.Mcp écrivant dans ces profils**. Le verrou des fichiers
protège les écritures concurrentes du carnet, pas un déploiement multi-réplicas
du cycle de vie. Aucun élargissement à un stockage distribué dans cette tranche.

## Validation

`Motivation.Tests` couvre limites, corruption, liens, concurrence entre instances,
rejeu, modifications, purge et choix déterministe (contexte, priorités, absence,
bornes, origines et inconnues). `Memory.Mcp.Tests` couvre les contrats HTTP,
Swagger, les droits, la sélection en lecture seule et les courses avec la suppression.

Le [laboratoire M12](https://github.com/gilldelia/J.E.S.S.I/blob/3250dcffe488e2efea0592a9e5e2f90c7c358d56/docs/qa/m12-e2e.md) ajoute un véritable redémarrage de
Memory.Mcp entre deux phases OAuth, trois Âmes, des tentatives d’accès croisées
et la suppression contrôlée. Il est lancé sous Linux Docker, jamais sur les
profils ni les souvenirs réels.
