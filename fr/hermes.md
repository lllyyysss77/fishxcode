# Utiliser FishXCode avec Hermes

::: info Présentation du projet
Hermes Agent est un agent IA généraliste de Nous Research. Il prend en charge le chat en CLI, l'appel d'outils, la mémoire, les skills, les passerelles et les tâches planifiées. Il peut se connecter aux services cloud officiellement pris en charge ou à n'importe quel endpoint compatible OpenAI.

- Site officiel : [https://hermes-agent.nousresearch.com](https://hermes-agent.nousresearch.com)
- Documentation : [https://hermes-agent.nousresearch.com/docs](https://hermes-agent.nousresearch.com/docs)
- GitHub : [https://github.com/NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)
:::

## Prérequis

- Clé API FishXCode ([Obtenir depuis la console](https://fishxcode.com/console/token))
- `git` disponible sur votre machine

## Installer Hermes

::: info Environnement requis
- macOS / Linux / WSL2
- L'installation via PowerShell est possible sous Windows, mais WSL2 reste recommandé
- L'installateur gère automatiquement Python, Node.js, ripgrep et ffmpeg
:::

::: code-group

```bash [Installateur officiel]
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
```

```powershell [Windows PowerShell]
irm https://res1.hermesagent.org.cn/install.ps1 | iex
```

:::

Après l'installation, rechargez la configuration de votre shell :

```bash
source ~/.zshrc
```

Si vous utilisez `bash`, exécutez :

```bash
source ~/.bashrc
```

Si vous utilisez Windows PowerShell, fermez simplement le terminal puis rouvrez-le.

## Configurer FishXCode

Hermes recommande officiellement d'utiliser `hermes model` pour la configuration interactive. Avec FishXCode, choisissez **Custom endpoint**, car FishXCode fournit une API compatible OpenAI.

Si vous souhaitez effectuer toute la configuration post-installation en une seule fois, vous pouvez aussi lancer :

```bash
hermes setup
```

Si vous souhaitez seulement configurer les permissions des outils, lancez :

```bash
hermes tools
```

### Méthode 1 : Configuration interactive avec `hermes model` (Recommandée)

Exécutez :

```bash
hermes model
```

Renseignez les champs comme suit :

- Provider : `Custom endpoint (self-hosted / VLLM / etc.)`
- API base URL : `https://fishxcode.com/v1`
- API key : votre token FishXCode
- Model name : `gpt-5.4`
- Context length : utilisez au minimum `65536`

Après la configuration, Hermes écrit le modèle, le provider et l'endpoint dans `~/.hermes/config.yaml`.

::: warning Important
Hermes attend un modèle avec au moins `64K` de contexte pour les workflows d'agent réels à plusieurs étapes. Pour un endpoint personnalisé, choisissez donc un modèle et une fenêtre de contexte conformes à cette exigence.
:::

### Méthode 2 : Modifier le fichier de configuration manuellement

Hermes utilise `~/.hermes/config.yaml` comme fichier principal. Si le dossier n'existe pas encore, créez-le d'abord :

```bash
mkdir -p ~/.hermes
touch ~/.hermes/config.yaml
touch ~/.hermes/.env
```

Ensuite, placez votre token dans `~/.hermes/.env` :

```bash
OPENAI_API_KEY=sk-your-fishxcode-token
```

Puis écrivez ceci dans `~/.hermes/config.yaml` :

```yaml
model:
  default: gpt-5.4
  provider: custom
  base_url: https://fishxcode.com/v1
```

::: tip Conseil
Pour les endpoints personnalisés, Hermes lit `provider`, `default` et `base_url` depuis `config.yaml`. La clé API peut être écrite directement dans `config.yaml`, ou placée dans `~/.hermes/.env` sous `OPENAI_API_KEY` comme ci-dessus. La solution `.env` est recommandée pour éviter de stocker la clé en clair.
:::

## Changer de modèle

Pour utiliser un autre modèle pris en charge par FishXCode, modifiez simplement `model.default` ou relancez `hermes model`.

Par exemple :

```yaml
model:
  default: claude-sonnet-4-5-20250929
  provider: custom
  base_url: https://fishxcode.com/v1
```

::: warning Remarque
Cela suppose que le modèle est disponible via l'endpoint compatible OpenAI de FishXCode et qu'il respecte l'exigence minimale de contexte de Hermes. Si vous n'êtes pas sûr de l'identifiant exact du modèle, consultez d'abord la page [Modèles pris en charge](/fr/models), puis renseignez le champ `default`.
:::

## Démarrer Hermes

Démarrez une session interactive :

```bash
hermes
```

Ou envoyez un message de test rapide :

```bash
hermes chat -q "Réponds en une phrase : FishXCode est connecté"
```

Si la configuration fonctionne déjà, vous pouvez aussi changer de modèle dans la session avec `/model`.

## Vérifier la configuration

Commencez par ces vérifications :

```bash
hermes doctor
```

```bash
hermes config check
```

```bash
hermes chat -q "Réponds uniquement par ok" -Q
```

Si la dernière commande renvoie une réponse normale, l'intégration FishXCode fonctionne.

## FAQ

### Pourquoi choisir `Custom endpoint` ?

Parce que Hermes traite toute API compatible OpenAI comme `provider: custom`. FishXCode correspond à ce modèle, donc aucun adaptateur spécifique à Hermes n'est nécessaire.

### Pourquoi `OPENAI_BASE_URL` ou `LLM_MODEL` ne fonctionnent-ils pas ?

Hermes a supprimé la prise en charge de ces anciennes variables d'environnement. Le modèle, le provider et l'endpoint sont désormais définis via `~/.hermes/config.yaml`.

### Que faire si la clé API est configurée mais que l'authentification échoue toujours ?

Vérifiez dans cet ordre :

1. Assurez-vous que `base_url` est `https://fishxcode.com/v1`
2. Assurez-vous que le token provient bien de la [console FishXCode](https://fishxcode.com/console/token)
3. Assurez-vous que `model.default` est un identifiant de modèle valide, par exemple `gpt-5.4`
4. Assurez-vous que le contexte du modèle est au moins de `65536`
5. Exécutez `hermes config check` et `hermes doctor` pour voir l'erreur exacte

### Puis-je installer Hermes directement sur Windows ?

Oui, l'installation via PowerShell est possible, mais WSL2 reste l'option la plus sûre pour la compatibilité et un flux de travail de type Unix plus stable.
