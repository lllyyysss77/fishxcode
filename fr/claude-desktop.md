# Utiliser FishXCode avec Claude Desktop

## Portée

Ce guide concerne le nouveau mode **Cowork / Code avec inférence tierce** dans Claude Desktop, documenté par Anthropic sous le nom `third-party inference`.

Utilisez cette méthode si vous voulez :

- utiliser **Cowork** dans Claude Desktop
- utiliser **Code** dans Claude Desktop
- faire passer les requêtes via FishXCode grâce à une API ou une passerelle personnalisée

::: warning Remarque
Il ne s'agit pas de l'ancien flux "connexion puis chat". Une fois l'inférence tierce activée, vous utiliserez surtout les onglets **Cowork** et **Code**.
:::

## Prérequis

Préparez les éléments suivants :

1. La dernière version de Claude Desktop
2. Un token FishXCode
3. Un token capable d'accéder aux modèles Claude

Obtenir le token :
- [https://fishxcode.com/console/token](https://fishxcode.com/console/token)

Modèles recommandés pour commencer :

- `claude-sonnet-4-5-20250929`
- `claude-opus-4-5-20251101`
- `claude-haiku-4-5-20251001`

## Méthode 1 : Configurer directement dans Claude Desktop (Recommandée)

Anthropic recommande d'utiliser le flux intégré de configuration d'inférence tierce dans l'application.

### 1. Activer le Developer mode

Dans Claude Desktop, allez dans :

```text
Help → Troubleshooting → Enable Developer mode
```

Après activation, vous devriez voir apparaître des entrées `Developer`.

### 2. Ouvrir la configuration d'inférence tierce

Ensuite, allez dans :

```text
Developer → Configure third-party inference
```

Cela ouvre le flux officiel de configuration d'Anthropic.

### 3. Choisir le mode Gateway

Sélectionnez :

```text
Gateway
```

FishXCode correspond au mode `gateway` dans ce flux.

### 4. Renseigner les paramètres FishXCode

Utilisez les valeurs suivantes :

- Gateway URL : `https://api.fishxcode.com`
- Authentication : `x-api-key`
- API Key : votre token FishXCode

Si l'interface demande une liste de modèles, commencez par :

```text
claude-sonnet-4-5-20250929
claude-opus-4-5-20251101
claude-haiku-4-5-20251001
```

Si une option permet d'activer `Code`, laissez-la activée.

### 5. Appliquer localement

À la fin, choisissez :

```text
Apply locally
```

Cela écrit la configuration dans le fichier local de Claude Desktop pour l'utilisateur courant.

Redémarrez ensuite Claude Desktop. Dans la plupart des cas, vous verrez alors l'espace de travail **Cowork / Code**.

## Méthode 2 : Gérer la configuration via le fichier local (Avancé)

Si vous voulez sauvegarder, migrer ou diagnostiquer la configuration, vous pouvez inspecter le fichier local.

Emplacements courants :

### macOS

```text
~/Library/Application Support/Claude-3p/claude_desktop_config.json
```

### Windows

```text
%APPDATA%\Claude-3p\claude_desktop_config.json
```

### Linux

```text
~/.config/Claude-3p/claude_desktop_config.json
```

::: tip Conseil
Le fichier local stocke la configuration d'inférence tierce sous `enterpriseConfig`. Le chemin le plus sûr consiste à configurer d'abord l'application, puis à sauvegarder le résultat généré, plutôt que d'écrire le JSON à la main.
:::

## Valeurs FishXCode recommandées

Si vous souhaitez vérifier manuellement les champs importants, concentrez-vous sur ceux-ci :

| Champ | Valeur recommandée |
| --- | --- |
| `inferenceProvider` | `gateway` |
| `inferenceGatewayBaseUrl` | `https://api.fishxcode.com` |
| `inferenceGatewayAuthScheme` | `x-api-key` |
| `inferenceGatewayApiKey` | votre token FishXCode |
| `inferenceModels` | liste de modèles Claude |
| `isClaudeCodeForDesktopEnabled` | `true` |

Liste de modèles recommandée :

```text
claude-sonnet-4-5-20250929
claude-opus-4-5-20251101
claude-haiku-4-5-20251001
```

## Utilisation

Une fois configuré, vous pouvez utiliser Claude Desktop pour :

- ouvrir **Cowork** pour les tâches générales, les documents et la collaboration sur fichiers
- ouvrir **Code** pour le développement, le terminal et les tâches d'ingénierie

Si le sélecteur de modèles est visible, commencez par :

- usage quotidien : `claude-sonnet-4-5-20250929`
- raisonnement approfondi : `claude-opus-4-5-20251101`
- tâches légères et rapides : `claude-haiku-4-5-20251001`

## FAQ

### Pourquoi ne puis-je pas trouver `Enable Developer mode` ?

Vérifiez d'abord :

1. Vous utilisez une version récente de Claude Desktop
2. Vous regardez bien la vraie barre de menus du système, et non seulement les contrôles de la fenêtre

Si l'entrée n'apparaît toujours pas après mise à jour, quittez complètement Claude Desktop puis relancez-le.

### Que faire si Windows affiche `Virtual Machine Platform not available` ?

Si Claude Desktop affiche :

```text
Claude's workspace requires the Virtual Machine Platform on Windows. Enable this feature, then restart.
```

Cela signifie que **Virtual Machine Platform** n'est pas activé sur Windows. Ouvrez PowerShell en tant qu'administrateur et exécutez :

```powershell
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

Une fois la commande terminée, redémarrez Windows puis rouvrez Claude Desktop.

### Pourquoi ne vois-je pas Cowork / Code après la configuration ?

Vérifiez dans cet ordre :

1. Le Developer mode est activé
2. `Configure third-party inference` a bien été complété
3. Vous avez sélectionné `Gateway`
4. `gatewayUrl` vaut bien `https://api.fishxcode.com`
5. Le token est valide et peut accéder aux modèles Claude
6. Claude Desktop a bien été complètement redémarré après la configuration

### Pourquoi l'URL de gateway n'est-elle pas `https://api.fishxcode.com/v1` ?

Parce que l'inférence tierce de Claude Desktop utilise une configuration de passerelle de style Anthropic, et non le chemin OpenAI-compatible `/v1/chat/completions`. Il faut donc utiliser l'URL racine de la passerelle :

```text
https://api.fishxcode.com
```

### Que faire si l'authentification échoue après configuration ?

Vérifiez d'abord :

1. que l'authentification est définie sur `x-api-key`
2. que le champ API Key contient directement votre token FishXCode
3. que le token est toujours valide et possède les autorisations requises

### Puis-je modifier le JSON local manuellement ?

Oui, mais ce n'est pas le meilleur choix pour une première configuration. Les champs liés à l'inférence tierce évoluent avec les versions de Claude Desktop, et le flux intégré reste plus stable et plus simple à corriger en cas de problème.
