# Utiliser FishXCode avec WorkBuddy

> Première utilisation ? Commencez par [créer votre compte et recharger](/fr/account), puis récupérez votre API Key dans [la console FishXCode](https://fishxcode.com/console/token).

## 1. Ouvrir le fichier de configuration WorkBuddy

WorkBuddy prend en charge l’intégration de modèles personnalisés via le fichier local `models.json`. Emplacement du fichier :

- macOS / Linux : `~/.workbuddy/models.json`
- Windows : `C:\Users\<votre-nom>\.workbuddy\models.json`

Si le dossier ou le fichier n’existe pas, créez-le d’abord.

## 2. Configurer les modèles FishXCode

La configuration des modèles tiers dans WorkBuddy utilise le protocole OpenAI Chat Completions compatible. Ici, `vendor: "OpenAI"` désigne la compatibilité de protocole, et non une limitation aux seuls modèles OpenAI ; les modèles Claude peuvent également être appelés via le point d’accès OpenAI compatible de FishXCode.

Écrivez le contenu suivant dans `~/.workbuddy/models.json` :

```json
{
  "models": [
    {
      "id": "gpt-5.5",
      "name": "FishXCode GPT-5.5",
      "vendor": "OpenAI",
      "url": "https://api.fishxcode.com/v1/chat/completions",
      "apiKey": "sk-你的FishXCode-Token",
      "maxInputTokens": 200000,
      "maxOutputTokens": 8192
    },
    {
      "id": "claude-opus-4-7",
      "name": "FishXCode Claude Opus 4.7",
      "vendor": "OpenAI",
      "url": "https://api.fishxcode.com/v1/chat/completions",
      "apiKey": "sk-你的FishXCode-Token",
      "maxInputTokens": 200000,
      "maxOutputTokens": 8192
    }
  ],
  "availableModels": [
    "gpt-5.5",
    "claude-opus-4-7"
  ]
}
```

::: warning Important
- Remplacez `sk-你的FishXCode-Token` par la vraie API Key générée dans la console FishXCode.
- `availableModels` doit inclure les identifiants définis dans `models[].id`; sinon WorkBuddy peut ne pas les afficher dans la liste.
- L’URL doit être le point d’accès complet Chat Completions : `https://api.fishxcode.com/v1/chat/completions`.
:::

## 3. Conserver aussi le plan Tencent

Si vous utilisez également le plan officiel Tencent, vous pouvez regrouper les modèles Tencent et FishXCode dans le même `models.json` :

```json
{
  "models": [
    {
      "id": "tc-code-latest",
      "name": "Auto",
      "vendor": "OpenAI",
      "url": "https://api.lkeap.cloud.tencent.com/plan/v3/chat/completions",
      "apiKey": "your-token-plan-api-key",
      "maxInputTokens": 100000,
      "maxOutputTokens": 4096
    },
    {
      "id": "hunyuan-2.0-instruct",
      "name": "Tencent HY 2.0 Instruct",
      "vendor": "OpenAI",
      "url": "https://api.lkeap.cloud.tencent.com/plan/v3/chat/completions",
      "apiKey": "your-token-plan-api-key",
      "maxInputTokens": 100000,
      "maxOutputTokens": 4096
    },
    {
      "id": "gpt-5.5",
      "name": "FishXCode GPT-5.5",
      "vendor": "OpenAI",
      "url": "https://api.fishxcode.com/v1/chat/completions",
      "apiKey": "sk-你的FishXCode-Token",
      "maxInputTokens": 200000,
      "maxOutputTokens": 8192
    },
    {
      "id": "claude-opus-4-7",
      "name": "FishXCode Claude Opus 4.7",
      "vendor": "OpenAI",
      "url": "https://api.fishxcode.com/v1/chat/completions",
      "apiKey": "sk-你的FishXCode-Token",
      "maxInputTokens": 200000,
      "maxOutputTokens": 8192
    }
  ],
  "availableModels": [
    "tc-code-latest",
    "hunyuan-2.0-instruct",
    "gpt-5.5",
    "claude-opus-4-7"
  ]
}
```

## 4. Redémarrer et vérifier

1. Enregistrez `models.json`
2. Quittez complètement WorkBuddy
3. Relancez WorkBuddy
4. Ouvrez la zone de sélection des modèles
5. Choisissez `gpt-5.5` ou `claude-opus-4-7`
6. Envoyez une question simple pour vérifier la réponse

## FAQ

### Je ne vois pas les nouveaux modèles

Vérifiez d’abord que `availableModels` contient bien l’identifiant correspondant, puis assurez-vous que `models.json` est un JSON valide.

### L’appel échoue ou une erreur rouge apparaît

Vérifiez que `url` pointe bien vers l’adresse complète `https://api.fishxcode.com/v1/chat/completions` et non vers `https://api.fishxcode.com/v1` uniquement.

### Échec d’authentification

Recopiez l’API Key depuis la console FishXCode et vérifiez qu’il n’y a ni espace superflu ni saut de ligne.
