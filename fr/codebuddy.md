# Utiliser FishXCode avec CodeBuddy

> Première utilisation ? Commencez par [créer votre compte et recharger](/fr/account), puis récupérez votre API Key dans [la console FishXCode](https://fishxcode.com/console/token).

## 1. Installer et lancer CodeBuddy Code

Ce guide configure l’outil en ligne de commande **CodeBuddy Code** de Tencent Cloud. Vérifiez d’abord que Node.js 18.0 ou supérieur est installé localement, puis installez-le :

```bash
npm install -g @tencent-ai/codebuddy-code
```

Vérifiez la version :

```bash
codebuddy --version
```

Lancez CodeBuddy Code :

```bash
codebuddy
```

Au premier démarrage, suivez les instructions pour terminer la connexion. Ensuite, vous pouvez exécuter `/model` dans le terminal pour vérifier que les modèles personnalisés sont bien chargés.

## 2. Ouvrir le fichier de configuration CodeBuddy

CodeBuddy Code prend en charge l’intégration de modèles personnalisés via le fichier local `models.json`. Emplacement du fichier :

- macOS / Linux : `~/.codebuddy/models.json`
- Windows : `C:\Users\<votre-nom>\.codebuddy\models.json`

Si le dossier ou le fichier n’existe pas, créez-le d’abord.

## 3. Ajouter la configuration FishXCode

La configuration des modèles tiers dans CodeBuddy Code utilise le protocole OpenAI Chat Completions compatible. Ici, `vendor: "OpenAI"` désigne la compatibilité du protocole, et non une limitation aux seuls modèles OpenAI ; les modèles Claude peuvent également être appelés via le point d’accès OpenAI compatible de FishXCode.

Écrivez le contenu suivant dans `~/.codebuddy/models.json` :

```json
{
  "models": [
    {
      "id": "gpt-5.5",
      "name": "FishXCode GPT-5.5",
      "vendor": "OpenAI",
      "apiKey": "sk-你的FishXCode-Token",
      "url": "https://api.fishxcode.com/v1/chat/completions"
    },
    {
      "id": "claude-opus-4-7",
      "name": "FishXCode Claude Opus 4.7",
      "vendor": "OpenAI",
      "apiKey": "sk-你的FishXCode-Token",
      "url": "https://api.fishxcode.com/v1/chat/completions"
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
- `availableModels` doit inclure les identifiants définis dans `models[].id`; sinon `/model` peut ne pas afficher les nouveaux modèles.
- Le point d’accès tiers doit être l’adresse complète Chat Completions : `https://api.fishxcode.com/v1/chat/completions`.
:::

## 4. Conserver le plan Tencent en parallèle

Si vous devez aussi conserver le plan officiel Tencent, vous pouvez ajouter les modèles FishXCode dans le même `models.json` :

```json
{
  "models": [
    {
      "id": "tc-code-latest",
      "name": "Auto",
      "vendor": "Tencent Cloud",
      "apiKey": "YOUR_TENCENT_CODING_PLAN_KEY",
      "url": "https://api.lkeap.cloud.tencent.com/coding/v3"
    },
    {
      "id": "gpt-5.5",
      "name": "FishXCode GPT-5.5",
      "vendor": "OpenAI",
      "apiKey": "sk-你的FishXCode-Token",
      "url": "https://api.fishxcode.com/v1/chat/completions"
    },
    {
      "id": "claude-opus-4-7",
      "name": "FishXCode Claude Opus 4.7",
      "vendor": "OpenAI",
      "apiKey": "sk-你的FishXCode-Token",
      "url": "https://api.fishxcode.com/v1/chat/completions"
    }
  ],
  "availableModels": [
    "tc-code-latest",
    "gpt-5.5",
    "claude-opus-4-7"
  ]
}
```

## 5. Redémarrer et choisir un modèle

1. Enregistrez `~/.codebuddy/models.json`
2. Quittez complètement CodeBuddy Code
3. Relancez `codebuddy`
4. Saisissez `/model`
5. Choisissez `gpt-5.5` ou `claude-opus-4-7`
6. Envoyez une invite de test, par exemple `Explique la structure du projet actuel`

## FAQ

### `/model` n’affiche pas les modèles

Vérifiez d’abord `availableModels`. L’identifiant du modèle doit apparaître à la fois dans `models[].id` et `availableModels`.

### Le modèle apparaît mais l’appel échoue

Vérifiez d’abord `url`. La configuration des modèles tiers FishXCode doit utiliser l’adresse complète `https://api.fishxcode.com/v1/chat/completions`.

### Échec d’authentification

Recopiez l’API Key depuis la console FishXCode et vérifiez qu’il n’y a ni espace superflu ni saut de ligne.
