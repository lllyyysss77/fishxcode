# Utiliser FishXCode avec QClaw

> Première utilisation ? Commencez par [créer votre compte et recharger](/fr/account), puis récupérez votre API Key dans [la console FishXCode](https://fishxcode.com/console/token).

## 1. Vérifier les paramètres de connexion

QClaw utilise de préférence le protocole OpenAI Compatible pour connecter des API de grands modèles tiers. Même lorsqu’un modèle Claude est utilisé, les paramètres doivent rester ceux d’une interface compatible OpenAI.

| Élément | Valeur |
| --- | --- |
| Protocol | `OpenAI Compatible` |
| Base URL | `https://fishxcode.com/v1` |
| API Key | API Key créée dans la console FishXCode |
| Modèle OpenAI | `gpt-5.5` |
| Modèle Claude | `claude-opus-4-7` |

Si QClaw demande un endpoint complet, utilisez :

```text
https://fishxcode.com/v1/chat/completions
```

## 2. Configurer depuis l’interface

Si votre version de QClaw propose une interface graphique, renseignez les champs comme suit :

```text
Provider: OpenAI Compatible
Base URL: https://fishxcode.com/v1
API Key: sk-你的FishXCode-Token
Model: claude-opus-4-7
```

Pour utiliser le modèle GPT, remplacez `Model` par :

```text
gpt-5.5
```

## 3. Configurer via `config.yaml`

Si votre version de QClaw utilise `~/.qclaw/config.yaml`, vous pouvez utiliser le modèle suivant :

```yaml
llm:
  provider: openai-compatible
  base_url: https://fishxcode.com/v1
  api_key: sk-你的FishXCode-Token
  model: claude-opus-4-7
  max_tokens: 8192
```

Pour passer au modèle OpenAI, il suffit de remplacer `model` :

```yaml
llm:
  provider: openai-compatible
  base_url: https://fishxcode.com/v1
  api_key: sk-你的FishXCode-Token
  model: gpt-5.5
  max_tokens: 8192
```

Si le client exige l’URL complète de l’interface, utilisez :

```yaml
llm:
  provider: openai-compatible
  url: https://fishxcode.com/v1/chat/completions
  api_key: sk-你的FishXCode-Token
  model: claude-opus-4-7
  max_tokens: 8192
```

::: warning Important
Les noms de champs peuvent varier selon les versions de QClaw, par exemple `base_url`, `baseURL`, `apiBase` ou `url`. Si la configuration ne prend pas effet après l’enregistrement, vérifiez d’abord le nom des champs et le chemin réel du fichier de configuration lu par QClaw.
:::

## 4. Configurer via les variables d’environnement

Si QClaw prend en charge les variables d’environnement, vous pouvez aussi utiliser :

::: code-group

```bash [Linux/macOS]
export OPENAI_BASE_URL="https://fishxcode.com/v1"
export OPENAI_API_KEY="sk-你的FishXCode-Token"
export QCLAW_MODEL="claude-opus-4-7"
qclaw
```

```powershell [Windows PowerShell]
$env:OPENAI_BASE_URL="https://fishxcode.com/v1"
$env:OPENAI_API_KEY="sk-你的FishXCode-Token"
$env:QCLAW_MODEL="claude-opus-4-7"
qclaw
```

:::

Pour utiliser le modèle GPT, remplacez `QCLAW_MODEL` par `gpt-5.5`.

## 5. Vérifier la configuration

Commencez par vérifier que l’API Key fonctionne via l’interface compatible OpenAI :

::: code-group

```bash [Modèle Claude]
curl https://fishxcode.com/v1/chat/completions \
  -H "content-type: application/json" \
  -H "authorization: Bearer sk-你的FishXCode-Token" \
  -d '{
    "model": "claude-opus-4-7",
    "messages": [
      { "role": "user", "content": "Présente-toi en une phrase" }
    ]
  }'
```

```bash [Modèle GPT]
curl https://fishxcode.com/v1/chat/completions \
  -H "content-type: application/json" \
  -H "authorization: Bearer sk-你的FishXCode-Token" \
  -d '{
    "model": "gpt-5.5",
    "messages": [
      { "role": "user", "content": "Présente-toi en une phrase" }
    ]
  }'
```

:::

Si curl échoue, QClaw échouera probablement aussi. Vérifiez d’abord l’API Key, les droits du modèle et la Base URL.

1. Enregistrez les paramètres ou `config.yaml`
2. Redémarrez QClaw
3. Sélectionnez `gpt-5.5` ou `claude-opus-4-7`
4. Envoyez une courte question, par exemple `Présente-toi en une phrase`
5. Une réponse normale indique que l’intégration fonctionne

## FAQ

### Erreur 404 ou URL incorrecte

Si vous renseignez une Base URL, utilisez `https://fishxcode.com/v1`. Si vous renseignez une URL complète, utilisez `https://fishxcode.com/v1/chat/completions`.

### Le modèle Claude est-il utilisable ?

Oui. Il faut que FishXCode prenne actuellement en charge ce modèle et que QClaw l’appelle via le protocole OpenAI Compatible. Le nom du modèle à renseigner est `claude-opus-4-7`.

### L’authentification échoue

Copiez à nouveau l’API Key, vérifiez l’absence d’espace supplémentaire, et assurez-vous que cette clé n’a pas été supprimée ou désactivée.
