> Copie publique d’un README de J.E.S.S.I. Seuls les README sont publiés ; les liens vers le code, les guides détaillés et les profils nécessitent l’accès au dépôt privé. Aucun de ces fichiers n’est exporté.

# Ancien client console Âme

Ce projet contient uniquement le client conversationnel historique : boucle
interactive, ancienne configuration `personality.json`, appels au LLM et à
l'API Memory, puis historique local. Il est conservé provisoirement pour
compatibilité et ne constitue pas le service Âme cible.

Le domaine moderne des personas, leurs permissions, leur évolution et leur
stockage de profils sont dans la bibliothèque `Ame`. `Memory.Mcp` dépend de
cette bibliothèque et ne référence pas cet exécutable.

Le client historique admet encore directement les messages dans Memory. Il ne
doit donc pas être utilisé à la place du parcours filtré `memory_perceive`.

Depuis la racine du dépôt :

```sh
dotnet run --project Ame.Console/Ame.Console.csproj
```
