# Utiliser FishXCode avec OpenClaw

::: info Présentation du projet
OpenClaw est une plateforme d'assistant IA personnel open-source et auto-hébergée qui connecte les applications de messagerie à des agents IA fonctionnant sur votre propre matériel. Conçue pour les développeurs et les utilisateurs avancés souhaitant des assistants IA autonomes sans céder le contrôle de leurs données.

- Site officiel : [https://openclaw.ai](https://openclaw.ai)
- Documentation : [https://docs.openclaw.ai](https://docs.openclaw.ai)
- GitHub : [https://github.com/openclaw/openclaw](https://github.com/openclaw/openclaw)
:::

## Prérequis

- OpenClaw installé (voir la section installation ci-dessous)
- Clé API FishXCode ([Obtenir depuis la console](https://fishxcode.com/console/token))

## Fonctionnalités principales

### Intégration multi-canaux

- **Couverture complète** : Supporte Lark (Feishu), Discord, Slack, Microsoft Teams et plus
- **Passerelle unique** : Gérez tous les canaux via un seul processus Gateway
- **Support vocal** : Interaction vocale sur macOS/iOS/Android
- **Interface Canvas** : Rendu d'interfaces Canvas interactives

### Auto-hébergement et sécurité des données

- **Entièrement auto-hébergé** : Fonctionne sur votre propre machine ou serveur
- **Open source** : Licence MIT, code entièrement transparent
- **Stockage local** : Contexte et compétences stockés localement, pas dans le cloud

### Capacités d'agent intelligent

- **Toujours actif** : Opération persistante en arrière-plan avec mémoire persistante
- **Tâches planifiées** : Supporte les tâches cron
- **Isolation des sessions** : Sessions isolées par agent/espace de travail/expéditeur
- **Routage multi-agents** : Collaboration entre plusieurs agents
- **Appel d'outils** : Support natif des appels d'outils et de l'exécution de code

## Installation

::: info Prérequis
- Clé API FishXCode
- Node.js 22+ requis pour les méthodes npm/git ; le one-liner curl gère automatiquement les dépendances
:::

::: code-group

```bash [curl (Recommandé)]
curl -fsSL https://openclaw.ai/install.sh | bash
```

```bash [npm]
npm install -g openclaw@latest
```

```bash [curl (mode git)]
curl -fsSL https://openclaw.ai/install.sh | bash -s -- --install-method git
```

```bash [Clonage manuel]
git clone https://github.com/openclaw/openclaw.git
cd openclaw && pnpm install && pnpm run build
pnpm run openclaw onboard
```

:::

Après l'installation, lancez l'assistant de configuration (déjà inclus dans les étapes de clonage manuel) :

```bash
openclaw onboard
```

## Configuration

### Emplacement du fichier de configuration

Le fichier de configuration OpenClaw se trouve à `~/.openclaw/openclaw.json` :

- **macOS** : `/Users/votre-utilisateur/.openclaw/openclaw.json`
- **Linux** : `/home/votre-utilisateur/.openclaw/openclaw.json`
- **Windows** : `C:\Users\votre-utilisateur\.openclaw\openclaw.json`

Si le fichier n'existe pas, créez-le d'abord :

::: code-group

```bash [macOS / Linux]
mkdir -p ~/.openclaw
touch ~/.openclaw/openclaw.json
```

```powershell [Windows (PowerShell)]
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.openclaw"
New-Item -ItemType File -Force -Path "$env:USERPROFILE\.openclaw\openclaw.json"
```

:::

::: tip Astuce
Si vous avez déjà exécuté `openclaw onboard`, le fichier de configuration sera généré automatiquement. Modifiez simplement le contenu existant.
:::

### Structure du fichier de configuration

La structure d'`openclaw.json` se compose de deux grandes parties :

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      // Configurer les fournisseurs de modèles IA
    }
  },
  "agents": {
    "defaults": {
      // Configurer le modèle par défaut, le répertoire de travail, etc.
    }
  }
}
```

- `models.providers` — Définit les fournisseurs de services (URL, clé, liste de modèles)
- `models.mode` — Régler sur `"merge"` pour fusionner la config personnalisée avec les défauts intégrés, **fortement recommandé**
- `agents.defaults.model.primary` — Modèle utilisé par défaut, format : `nom-provider/id-modèle`
- `api` — Type de protocole API : `"anthropic-messages"` pour les modèles Anthropic, `"openai-responses"` pour les modèles compatibles OpenAI

### Méthodes de configuration

#### Configurer les modèles Anthropic (Claude)

Ajoutez le contenu suivant dans `openclaw.json` :

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "fishxcode-anthropic": {
        "baseUrl": "https://api.fishxcode.com",
        "apiKey": "sk-votre-token-fishxcode",
        "api": "anthropic-messages",
        "models": [
          {
            "id": "claude-opus-4-6",
            "name": "Claude Opus 4.6",
            "input": ["text", "image"],
            "contextWindow": 200000,
            "maxTokens": 8192,
            "reasoning": false
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "fishxcode-anthropic/claude-opus-4-6"
      }
    }
  }
}
```

::: warning Important
- Remplacez `sk-votre-token-fishxcode` par votre token réel obtenu depuis la [console FishXCode](https://fishxcode.com/console/token)
- **Pour le protocole Anthropic, le `baseUrl` ne doit PAS inclure `/v1`** — le SDK ajoute automatiquement le chemin
:::

#### Configurer les modèles OpenAI (GPT)

Lorsque vous appelez des modèles OpenAI via FishXCode, le champ `api` doit être `openai-responses` :

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "fishxcode-openai": {
        "baseUrl": "https://api.fishxcode.com/v1",
        "apiKey": "sk-votre-token-fishxcode",
        "api": "openai-responses",
        "models": [
          {
            "id": "gpt-5",
            "name": "GPT-5",
            "input": ["text", "image"],
            "contextWindow": 200000,
            "maxTokens": 16384,
            "reasoning": true
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "fishxcode-openai/gpt-5"
      }
    }
  }
}
```

::: tip
**Le protocole OpenAI nécessite `/v1`**, soit `https://api.fishxcode.com/v1`. Cela est dû au fait que les deux SDK ont des logiques de concaténation de chemin différentes.
:::

#### Configurer Anthropic + OpenAI simultanément (Recommandé)

Ajoutez les deux providers côte à côte dans `models.providers` pour utiliser les modèles des deux familles :

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "fishxcode-anthropic": {
        "baseUrl": "https://api.fishxcode.com",
        "apiKey": "sk-votre-token-fishxcode",
        "api": "anthropic-messages",
        "models": [
          {
            "id": "claude-opus-4-6",
            "name": "Claude Opus 4.6",
            "input": ["text", "image"],
            "contextWindow": 200000,
            "maxTokens": 8192,
            "reasoning": false
          },
          {
            "id": "claude-sonnet-4-5-20250929",
            "name": "Claude Sonnet 4.5",
            "input": ["text", "image"],
            "contextWindow": 200000,
            "maxTokens": 8192,
            "reasoning": false
          }
        ]
      },
      "fishxcode-openai": {
        "baseUrl": "https://api.fishxcode.com/v1",
        "apiKey": "sk-votre-token-fishxcode",
        "api": "openai-responses",
        "models": [
          {
            "id": "gpt-5",
            "name": "GPT-5",
            "input": ["text", "image"],
            "contextWindow": 200000,
            "maxTokens": 16384,
            "reasoning": true
          },
          {
            "id": "gpt-5-codex",
            "name": "GPT-5 Codex",
            "input": ["text", "image"],
            "contextWindow": 200000,
            "maxTokens": 16384,
            "reasoning": true
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "fishxcode-anthropic/claude-opus-4-6",
        "fallbacks": [
          "fishxcode-anthropic/claude-sonnet-4-5-20250929",
          "fishxcode-openai/gpt-5"
        ]
      }
    }
  }
}
```

### Détails des champs clés

| Champ | Signification | Anthropic (Claude) | OpenAI (GPT) |
| --- | --- | --- | --- |
| `baseUrl` | Adresse du proxy API | `https://api.fishxcode.com` | `https://api.fishxcode.com/v1` |
| `apiKey` | Votre clé API | `sk-votre-token-fishxcode` | `sk-votre-token-fishxcode` |
| `api` | Type de protocole API | `anthropic-messages` | `openai-responses` |
| `mode` | Mode de fusion de config | `merge` (recommandé) | `merge` (recommandé) |
| `models[].id` | ID du modèle | `claude-opus-4-6` | `gpt-5` |
| `model.primary` | Modèle par défaut | `fishxcode-anthropic/claude-opus-4-6` | `fishxcode-openai/gpt-5` |
| `reasoning` | Activer le mode raisonnement | `false` (selon le modèle) | `true` (GPT-5.x supporté) |

## Vérifier la configuration

Exécutez la commande suivante pour confirmer que la configuration fonctionne :

```bash
openclaw
```

Vérifier la liste des modèles :

```bash
openclaw models list
```

Vérifier le statut des modèles et l'authentification :

```bash
openclaw models status
```

Diagnostic complet :

```bash
openclaw doctor
```

## Démarrer le service

Une fois la configuration terminée, démarrez OpenClaw :

```bash
openclaw start
```

Une fois démarré, vous pouvez interagir avec l'assistant IA via les canaux configurés.

Redémarrer la passerelle :

```bash
openclaw gateway restart
```

## Dépannage

### 403 Bloqué

**Symptôme** : Le provider est configuré, la requête curl directe à l'API retourne 200, mais les requêtes depuis OpenClaw reçoivent un 403 "Your request was blocked".

**Cause** : OpenClaw utilise `@anthropic-ai/sdk` en coulisses, qui envoie des requêtes avec le User-Agent officiel du SDK (ex : `Anthropic/JS 0.73.0`). Certains CDN ou WAF bloquent ce UA.

**Solution** : Ajoutez un champ `headers` dans la config du provider pour remplacer le UA :

```json
{
  "fishxcode-anthropic": {
    "baseUrl": "https://api.fishxcode.com",
    "apiKey": "votre-api-key",
    "api": "anthropic-messages",
    "headers": {
      "User-Agent": "Mozilla/5.0"
    },
    "models": [...]
  }
}
```

### Ne pas inclure /v1 dans baseUrl (Protocole Anthropic)

**Symptôme** : La requête retourne 404, et les logs montrent que le chemin est devenu `/v1/v1/messages`.

**Cause** : Le SDK Anthropic ajoute automatiquement `/v1/messages` au baseURL. Si votre baseUrl contient déjà `/v1`, le chemin réel devient dupliqué.

**Solution** : Pour le protocole Anthropic, n'indiquez que le domaine dans baseUrl, sans `/v1` :

```json
{
  "baseUrl": "https://api.fishxcode.com"
}
```

::: tip
Le protocole OpenAI nécessite `/v1`, soit `https://api.fishxcode.com/v1`. Cela est dû au fait que les deux SDK ont des logiques de concaténation de chemin différentes.
:::

### Le champ api n'accepte que trois valeurs

**Symptôme** : Au démarrage, affiche "Config invalid", ou le provider configuré n'apparaît pas dans la liste des modèles.

**Cause** : OpenClaw valide strictement le champ `api`, n'acceptant que ces trois valeurs :

| Valeur | Protocole |
| --- | --- |
| `anthropic-messages` | Anthropic Messages API |
| `openai-completions` | OpenAI Chat Completions |
| `openai-responses` | OpenAI Responses API |

Des valeurs comme `openai-chat`, `openai`, `anthropic`, etc. provoqueront des erreurs.

**Solution** : Lors de l'utilisation de FishXCode, utilisez `anthropic-messages` pour les modèles Claude et `openai-responses` pour les modèles GPT.

### Réponse vide avec openai-completions (Ne pas utiliser pour les modèles GPT)

**Symptôme** : `api` est réglé sur `openai-completions`, la requête réussit (`isError=false` dans les logs), mais l'interface affiche un message vide.

**Cause** : OpenClaw gère les flux de messages en interne au format Anthropic. Les réponses au format OpenAI de `openai-completions` peuvent ne pas être correctement mappées dans certains cas.

**Solution** : Pour appeler des modèles GPT via FishXCode, utilisez `openai-responses` plutôt que `openai-completions`.

### Les modifications de configuration ne prennent pas effet

**Symptôme** : `openclaw.json` modifié, mais OpenClaw utilise toujours l'ancienne configuration.

**Cause** : OpenClaw a deux endroits où les configurations de providers doivent être synchronisées :

```
~/.openclaw/openclaw.json              → models.providers
~/.openclaw/agents/main/agent/models.json → providers
```

Ne modifier qu'un seul endroit peut entraîner des problèmes.

**Solution** : Après modification, confirmez avec :

```bash
openclaw models status
```

Ou redémarrez la passerelle OpenClaw :

```bash
openclaw gateway restart
```

### Erreurs de format JSON

Erreurs courantes de format JSON :

- **Virgule en trop** : Le dernier élément ne peut pas avoir de virgule finale
- **Virgule manquante** : Deux paires clé-valeur adjacentes doivent être séparées par une virgule
- **Problèmes de guillemets** : JSON n'accepte que les guillemets doubles anglais `"`, pas les guillemets français ou simples
- **Parenthèses non appariées** : Chaque `{` doit avoir un `}` correspondant, chaque `[` doit avoir un `]` correspondant

Validez le format avec :

```bash
python3 -m json.tool ~/.openclaw/openclaw.json
```

### Référence des commandes de diagnostic

| Commande | Utilité |
| --- | --- |
| `openclaw models status` | Voir le statut des modèles et l'authentification |
| `openclaw models list` | Voir la liste des modèles configurés |
| `openclaw doctor` | Diagnostic complet |
| `openclaw gateway restart` | Redémarrer la passerelle |

::: tip Stratégie de débogage
Utilisez d'abord curl pour confirmer que l'API FishXCode elle-même fonctionne normalement, puis vérifiez ce qui est différent dans les requêtes envoyées par OpenClaw (UA, chemin, format).
:::
