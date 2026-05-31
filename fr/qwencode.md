# Utiliser FishXCode avec Qwen Code

## Installation

::: code-group

```bash [pnpm]
pnpm install -g @qwen-code/qwen-code
```

```bash [npm]
npm install -g @qwen-code/qwen-code
```

```bash [yarn]
yarn global add @qwen-code/qwen-code
```

```bash [bunx]
bunx --global @qwen-code/qwen-code
```

:::

## Configuration des Variables d'Environnement

::: code-group

```bash [Linux/macOS]
export OPENAI_API_KEY="sk-xxx"
export OPENAI_BASE_URL="https://api.fishxcode.com/v1"
export OPENAI_MODEL="gpt-5"
```

```powershell [Windows PowerShell]
$env:OPENAI_API_KEY="sk-xxx"
$env:OPENAI_BASE_URL="https://api.fishxcode.com/v1"
$env:OPENAI_MODEL="gpt-5"
```

:::

::: warning Important
Remplacez `sk-xxx` par votre token de la [Console FishXCode](https://fishxcode.com/console/token).
:::

## Lancement Direct

```bash
cd mon-projet
qwen
```

## Configuration Persistante

Ajoutez les variables à votre fichier de configuration shell :

- **Linux/macOS** : `~/.bashrc` ou `~/.zshrc`
- **Windows PowerShell** : `$PROFILE`
