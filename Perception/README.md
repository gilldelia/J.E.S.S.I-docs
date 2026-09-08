> Copie publique d’un README de J.E.S.S.I. Seuls les README sont publiés ; les liens vers le code, les guides détaillés et les profils nécessitent l’accès au dépôt privé. Aucun de ces fichiers n’est exporté.

# Perception

`Perception` est le sas cognitif optionnel placé entre une source réelle et
`Memory`. Il évalue chaque événement fourni avec une politique uniforme et
retourne une décision explicable. Il ne stocke rien et ne dépend ni de Qdrant,
ni d'un LLM, ni de `Memory`.

Le noyau actuel fournit :

- les modes `Off`, `RecallOnly`, `Explicit` et `Adaptive` ;
- des trames source regroupées en événements cohérents selon des frontières
  explicites, temporelles, techniques ou de changement de contexte ;
- un identifiant d'événement déterministe, dérivé des identifiants de trame,
  afin de permettre le rejeu ;
- des signaux bornés avec confiance, dérivation, provenance et preuves ;
- une extraction locale configurable par règles de phrases françaises et
  anglaises, sans appel réseau ni LLM ;
- le contrat métier d'un classifieur sémantique optionnel, exprimé uniquement
  avec les canaux et origines de Perception ;
- un score de saillance pondéré et configurable ;
- les décisions `Bypassed`, `Ephemeral` et `AdmitShortTerm` ;
- le détail des contributions au score ;
- un pipeline `segmentation -> extraction -> décision` sans persistance.

Les règles locales sont volontairement modestes : elles amorcent un système
testable et explicable. Elles ne prétendent pas comprendre tout le langage et
ne calculent pas encore la répétition, qui nécessite un historique.

`Perception` possède le contrat du classement sémantique et l'interprétation
déterministe des scores proposés par un modèle : validation des valeurs,
rejet des informations vagues ou transitoires, renforcement d'une correction
identitaire et création des preuves bornées. Il ne contient aucune
implémentation réseau. Le prompt, le transport HTTP, le JSON et l'identité de
l'éventuel LLM local restent dans `Memory.Mcp`. Le modèle propose des signaux ;
la politique de Perception reste seule responsable de leur admission dans le
calcul de saillance.

Le contrat ne classe jamais une modalité à partir du texte : le connecteur
source doit renseigner le canal réellement perçu.

`PerceptionDisposition.AdmitShortTerm` est une autorisation d'admission, pas
une écriture. L'adaptateur optionnel est porté par `Memory.Mcp` : son outil
`memory_perceive` transmet uniquement les événements admis au contrat HTTP de
Memory. `memory_remember` reste disponible pour une admission volontaire qui
contourne Perception.

Voir `docs/architecture/cognitive-target.md` pour les frontières et les étapes
suivantes.
