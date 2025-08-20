# Créez votre propre client MCP local avec LlamaIndex

Ce dépôt présente un exemple de **client MCP (Model Context Protocol) local** avec LlamaIndex. Le client se connecte à un serveur MCP local (ex. exposition d’outils comme une base SQLite) et permet d’interagir en langage naturel via des agents capables d’appeler ces outils — le tout en local.

## Prérequis

- **Python** ≥ 3.12
- **uv** (gestionnaire de dépendances Python). Installation : voir la documentation officielle d’uv
- **Ollama** pour exécuter un LLM local. Téléchargez et installez depuis le site d’Ollama (`https://ollama.com`), puis tirez un modèle :
  - `ollama pull llama3` (ou un autre modèle comme `qwen2.5`)

## Installation

Synchronisez les dépendances :

```sh
uv sync
```

## Démarrage rapide

1) Démarrer le serveur MCP local (ex. serveur de démonstration SQLite fourni) :

```sh
uv run server.py --server_type=sse
```

2) Utiliser un client

- Option A — Notebook : ouvrez `ollama_client.ipynb` et exécutez les cellules pour interagir avec l’agent.
- Option B — Exemple Python minimal avec LlamaIndex + Ollama :

```python
from llama_index.llms.ollama import Ollama
from llama_index.tools.mcp import MCPSseToolSpec
from llama_index.core.agent import ReActAgent

# 1) Définir le LLM local (Ollama doit être installé et un modèle disponible)
llm = Ollama(model="llama3")

# 2) Se connecter au serveur MCP en SSE
tool_spec = MCPSseToolSpec(server_url="http://localhost:8000/sse")
tools = tool_spec.to_tool_list()

# 3) Créer un agent LlamaIndex et chatter
agent = ReActAgent.from_tools(tools=tools, llm=llm)
response = agent.chat("Montre-moi les tables SQLite disponibles et décris les colonnes.")
print(response)
```

> Remarques :
> - Adaptez `model="llama3"` au modèle Ollama que vous avez tiré.
> - Adaptez l’URL `server_url` si vous lancez le serveur sur un autre port/hôte.

## Structure du projet

- `server.py` : serveur MCP local d’exemple (expose des outils comme SQLite)
- `ollama_client.ipynb` : notebook d’exemple côté client
- `pyproject.toml` : configuration des dépendances (gérées par `uv`)
- `README.md` : ce guide

## Dépannage

- **uv introuvable** : installez `uv` puis relancez `uv sync`.
- **Ollama / modèle introuvable** : exécutez `ollama pull <nom_du_modèle>` et réessayez.
- **Conflit de port** (par ex. `:8000` déjà utilisé) : lancez le serveur sur un autre port et mettez à jour `server_url` côté client.
- **Connexion SSE échouée** : vérifiez que le serveur tourne et que l’URL est correcte.

## Contribution

Les contributions sont bienvenues. Ouvrez une issue pour discuter d’une amélioration ou proposez une pull request.

