# Tutoriel ZCF

[ZCF](https://github.com/UfoMiao/zcf) est un outil d'initialisation zéro-configuration pour Claude Code / Codex. Une seule commande `zcf init` gère la configuration de l'API, l'import des workflows, l'intégration des services MCP et toutes les initialisations nécessaires.

> Pas encore de compte ? Complétez d'abord la [Création de compte](/fr/account) pour obtenir votre clé API.

---

## Paramètres courants

### Configuration API

| Paramètre | Description |
|---|---|
| `-p custom` | Utiliser un fournisseur personnalisé (méthode utilisée par FishXCode) |
| `-t api_key` | Écriture équivalente, même effet que `-p custom` |
| `-k "sk-xxx"` | Clé API |
| `-u "https://api.fishxcode.com/"` | URL de base FishXCode |
| `-M "claude-sonnet-4-5-20250929"` | Modèle principal |
| `-H "claude-haiku-4-5-20251001"` | Modèle rapide |

Exemple avec modèles spécifiés :

```bash
npx zcf i -s -t api_key -k "sk-xxx" -u "https://api.fishxcode.com/" \
  -M "claude-sonnet-4-5-20250929" \
  -H "claude-haiku-4-5-20251001"
```

### Workflows

| Paramètre | Description |
|---|---|
| `-w all` | Installer tous les workflows |
| `-w sixStepsWorkflow,gitWorkflow` | Installer les workflows spécifiés |
| `-w skip` | Ignorer |

Workflows disponibles : `commonTools` / `sixStepsWorkflow` / `featPlanUx` / `gitWorkflow` / `bmadWorkflow`

### Services MCP

| Paramètre | Description |
|---|---|
| `-m all` | Installer tous les MCP |
| `-m context7,open-websearch` | Installer les services spécifiés |
| `-m skip` | Ignorer |

Services disponibles : `context7` / `open-websearch` / `spec-workflow` / `mcp-deepwiki` / `Playwright` / `exa` / `serena`

### Autres options

| Paramètre | Description |
|---|---|
| `-g fr` | Définir uniformément l'interface, les modèles et la sortie IA en français |
| `-s` | Mode non-interactif, ignorer toutes les invites |
| `-r backup` | Stratégie de traitement de la configuration (`backup` / `merge` / `docs-only` / `skip`) |
| `-x false` | Ne pas installer la barre d'état CCometixLine |

---

## Commandes de workflow

Une fois l'installation terminée, les commandes slash suivantes sont disponibles dans Claude Code :

| Commande | Description |
|---|---|
| `/zcf:workflow` | Workflow de développement six phases (Recherche → Idée → Plan → Exécution → Optimisation → Révision) |
| `/zcf:feat` | Développement de nouvelles fonctionnalités avec planification et design UI/UX |
| `/zcf:git-commit` | Génération automatique de messages de commit Git |
| `/zcf:git-rollback` | Rollback interactif vers des versions antérieures |
| `/zcf:git-worktree` | Gestion des Git Worktrees |
| `/zcf:bmad-init` | Workflow de développement agile entreprise |

---

## Description des services MCP

MCP (Model Context Protocol) permet à Claude Code d'accéder à des outils et services externes. ZCF intègre les services suivants :

| Service | Description | Clé requise |
|---|---|---|
| `context7` | Documentation et exemples de code à jour pour les bibliothèques | Non |
| `open-websearch` | Recherche web DuckDuckGo/Bing/Brave | Non |
| `spec-workflow` | Workflow structuré Exigences → Design → Implémentation | Non |
| `mcp-deepwiki` | Consultation de documentation de dépôts GitHub | Non |
| `Playwright` | Automatisation du navigateur | Non |
| `exa` | Recherche sémantique Exa AI | Oui (`EXA_API_KEY`) |
| `serena` | Assistant IDE, récupération sémantique de code | Non |

### Détail de chaque service

**context7** — Consulte la documentation et les exemples de code les plus récents de n'importe quelle bibliothèque, évitant que l'IA n'utilise des API obsolètes :
```
Consulte la documentation et les exemples les plus récents du hook React useState
```

**open-websearch** — Recherche multi-moteurs (DuckDuckGo, Bing, Brave), sans clé API requise :
```
Recherche les nouveautés de TypeScript 5.0
```

**spec-workflow** — Workflow structuré de la conception à l'implémentation, incluant analyse des exigences, conception technique et découpage en tâches :
```bash
npx -y @pimzino/spec-workflow-mcp@latest --dashboard  # Lance le tableau de bord visuel
```

**mcp-deepwiki** — Consultation de la documentation d'un dépôt GitHub :
```
Consulte la documentation de l'API Composition du dépôt vuejs/core
```

**Playwright** — Contrôle du navigateur pour captures d'écran, remplissage de formulaires et interactions simulées. Un téléchargement du navigateur est requis à la première exécution.

**exa** — Recherche sémantique web Exa AI, nécessite une clé API :
```bash
export EXA_API_KEY="your-api-key"  # Obtenez-la sur dashboard.exa.ai
```

**serena** — Récupération sémantique de code et suggestions d'édition intelligentes, capacités similaires à un IDE.

### Emplacement des fichiers de configuration

Après installation par ZCF, la configuration MCP est écrite dans `~/.claude/settings.json` :

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@context-labs/context7"]
    },
    "open-websearch": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-open-websearch"]
    }
  }
}
```

::: tip Utilisateurs Windows
ZCF corrige automatiquement les formats de chemins Windows. En cas de problème de connexion MCP, exécutez `npx zcf` → sélectionnez `4. Configurer MCP` pour une correction automatique.
:::

---

## Mise à jour

```bash
npx zcf update
# ou
npx zcf u -s -g fr
```

La mise à jour synchronise uniquement les modèles de workflows et les prompts — **la configuration API n'est pas modifiée**.

---

## Questions fréquentes

### Mon settings.json existant sera-t-il écrasé ?

ZCF effectue automatiquement une sauvegarde dans `~/.claude/backup/YYYY-MM-DD_HH-mm-ss/` avant toute modification, et propose quatre stratégies de traitement :

```bash
npx zcf i -s -r backup    # Sauvegarder et écraser (par défaut)
npx zcf i -s -r merge     # Fusion intelligente
npx zcf i -s -r docs-only # Mettre à jour uniquement les documents de workflow
npx zcf i -s -r skip      # Ignorer sans modifier
```

### Comment revenir à une configuration manuelle ?

Le `settings.json` généré par ZCF est au format standard et peut être édité directement. Consultez la [configuration de Claude Code](/fr/start).

### Version Node.js requise

ZCF nécessite Node.js >= 18 :

```bash
node --version
```
