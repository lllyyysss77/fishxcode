# Using FishXCode with Hermes

::: info Project Introduction
Hermes Agent is a general-purpose AI agent from Nous Research. It supports CLI chat, tool calling, memory, skills, gateways, and scheduled tasks. It can connect to officially supported cloud services or any OpenAI-compatible endpoint.

- Official Website: [https://hermes-agent.nousresearch.com](https://hermes-agent.nousresearch.com)
- Documentation: [https://hermes-agent.nousresearch.com/docs](https://hermes-agent.nousresearch.com/docs)
- GitHub: [https://github.com/NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)
:::

## Prerequisites

- Hermes installed
- FishXCode API Key ([Get from Console](https://fishxcode.com/console/token))

## Install Hermes

::: info Environment Requirements
- macOS / Linux / WSL2
- Native Windows is not supported; WSL2 is recommended
:::

```bash
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
```

After installation, reload your shell config:

```bash
source ~/.zshrc
```

If you use `bash`, run:

```bash
source ~/.bashrc
```

## Configure FishXCode

Hermes officially recommends using `hermes model` for interactive setup. For FishXCode, choose **Custom endpoint** because FishXCode provides an OpenAI-compatible API.

### Option 1: Interactive setup with `hermes model` (Recommended)

Run:

```bash
hermes model
```

Fill in the prompts like this:

- Provider: `Custom endpoint (self-hosted / VLLM / etc.)`
- API base URL: `https://fishxcode.com/v1`
- API key: your FishXCode token
- Model name: `gpt-5`
- Context length: leave blank, or fill in the actual context size

After setup, Hermes writes the model, provider, and endpoint into `~/.hermes/config.yaml`.

### Option 2: Edit the config file manually

Hermes uses `~/.hermes/config.yaml` as its main config file. If the directory does not exist yet, create it first:

```bash
mkdir -p ~/.hermes
touch ~/.hermes/config.yaml
touch ~/.hermes/.env
```

Then put your token into `~/.hermes/.env`:

```bash
OPENAI_API_KEY=sk-your-fishxcode-token
```

Then write the following into `~/.hermes/config.yaml`:

```yaml
model:
  default: gpt-5
  provider: custom
  base_url: https://fishxcode.com/v1
```

::: tip Tip
For custom endpoints, Hermes reads `provider`, `default`, and `base_url` from `config.yaml`. The API key can be written directly in `config.yaml`, or placed in `~/.hermes/.env` as `OPENAI_API_KEY` like above. Using `.env` is recommended to avoid storing the key in plain text.
:::

## Switch Models

To use another model supported by FishXCode, just change `model.default` or run `hermes model` again.

For example:

```yaml
model:
  default: claude-sonnet-4-5-20250929
  provider: custom
  base_url: https://fishxcode.com/v1
```

::: warning Note
This assumes the model is available through FishXCode's OpenAI-compatible endpoint. If you are not sure about the exact model ID, check the [Supported Models](/en/models) page first, then fill in the `default` field.
:::

## Start Using Hermes

Start an interactive session:

```bash
hermes
```

Or send a quick test message:

```bash
hermes chat "Reply in one sentence: FishXCode is connected"
```

If the configuration is already working, you can also switch to another configured model inside the session with `/model`.

## Verify Configuration

Run these checks first:

```bash
hermes doctor
```

```bash
hermes config check
```

```bash
hermes chat "Please reply with ok only"
```

If the last command returns a normal response, the FishXCode integration is working.

## FAQ

### Why choose `Custom endpoint`?

Because Hermes treats any OpenAI-compatible API as `provider: custom`. FishXCode fits that pattern, so you do not need a Hermes-specific adapter.

### Why don't `OPENAI_BASE_URL` or `LLM_MODEL` work?

Hermes has removed support for those legacy environment variables. Model, provider, and endpoint settings now come from `~/.hermes/config.yaml`.

### What if the API key is configured but authentication still fails?

Check in this order:

1. Make sure `base_url` is `https://fishxcode.com/v1`
2. Make sure the token comes from the [FishXCode Console](https://fishxcode.com/console/token)
3. Make sure `model.default` is a valid model ID such as `gpt-5`
4. Run `hermes config check` and `hermes doctor` for the exact error

### Can I install Hermes directly on Windows?

As of April 22, 2026, the official Hermes documentation still states that native Windows is not supported. Use WSL2 instead.
