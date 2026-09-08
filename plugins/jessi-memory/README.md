> Copie publique d’un README de J.E.S.S.I. Seuls les README sont publiés ; les liens vers le code, les guides détaillés et les profils nécessitent l’accès au dépôt privé. Aucun de ces fichiers n’est exporté.

# Jessi Memory plugin

Private Codex/ChatGPT continuity backed by `https://memory.jessi-ai.com/mcp`.

The plugin currently contains:

- the remote OAuth MCP configuration used by Codex;
- a continuity skill that routes each real user turn through Perception and recalls context only when useful.

## ChatGPT connection still required

ChatGPT does not reuse Codex's local `.mcp.json`. In ChatGPT Developer Mode, register
`https://memory.jessi-ai.com/mcp`, complete OAuth, and copy the resulting identifier beginning
with `plugin_asdk_app`. That identifier will be added to `.app.json` before the ChatGPT variant
is installed.
