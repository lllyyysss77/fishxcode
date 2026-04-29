# FAQ

## General

### What is FishXCode?

FishXCode is an AI Coding relay that supports Claude and Codex models across multiple platforms.

### Which tools are supported?

Currently supported tools:

- **Claude Code** — Anthropic's official CLI
- **OpenAI Codex** — OpenAI's coding assistant
- **RooCode** — AI coding extension for VS Code
- **Qwen Code** — Alibaba's Qwen-based coding tool
- **Droid CLI** — Lightweight AI coding CLI
- **OpenCode** — Open-source AI coding terminal tool

### How is my data handled?

FishXCode acts solely as an API relay service. Your code and conversations are not stored on our servers. All requests are forwarded directly to the respective model providers.

::: tip
For sensitive projects, we recommend reviewing each model provider's privacy policy before use.
:::

## Account & Token

### How do I sign up?

Visit [fishxcode.com/register](https://fishxcode.com/register?aff=9CTW) and follow the prompts to create an account.

### How do I get an API token?

After signing in, go to the [Token Management page](https://fishxcode.com/console/token) in the console to generate a new token.

### How long does a token last?

Tokens remain valid until you manually delete or regenerate them. We recommend rotating tokens periodically for security.

### What are the usage quota limits?

Every user receives a usage quota. The exact amount adjusts dynamically based on platform resources. Check the console for your current usage.

::: warning
Once your quota is exhausted, requests will be rejected. Plan your usage accordingly.
:::

## Tool Configuration

### My environment variables don't seem to work

Check these common issues:

1. **Terminal not restarted** — After editing `.bashrc` / `.zshrc`, run `source ~/.bashrc` or open a new terminal
2. **Typos in variable names** — Names are case-sensitive; `ANTHROPIC_BASE_URL` is not the same as `Anthropic_Base_Url`
3. **Extra quotes or spaces** — Make sure there are no stray spaces around the value in `export KEY="value"`

::: details Quick diagnostic commands
```bash
# Verify that variables are set
echo $ANTHROPIC_BASE_URL
echo $ANTHROPIC_AUTH_TOKEN

# Empty output means the variable is not set
```
:::

### Where are configuration files located?

| Tool | Config Location |
|------|----------------|
| Claude Code | Environment variables |
| Codex | `~/.codex/config.toml` and `~/.codex/auth.json` |
| RooCode | VS Code Settings JSON |
| Qwen Code | Environment variables |

### I can't connect to FishXCode

1. Confirm the `BASE_URL` is set to `https://fishxcode.com/` (note the trailing `/`)
2. Verify that `fishxcode.com` is reachable from your network
3. If you're behind a corporate proxy, ensure your proxy settings are configured correctly

## Model Selection

### How do I choose the right model?

Pick based on your use case:

| Use Case | Recommended Model | Why |
|----------|-------------------|-----|
| Everyday coding | `claude-sonnet-4-5-20250929` | Good balance of capability and speed |
| Quick completions | `claude-3-5-haiku-20241022` | Fast response times |
| Complex architecture | `claude-sonnet-4-5-20250514` | Strong reasoning ability |

### What are the differences between models?

- **Sonnet series** — Well-rounded, suitable for most coding tasks
- **Haiku series** — Lightweight and fast, ideal for simple completions and formatting
- For detailed capability comparisons, refer to each provider's official documentation

### How do I switch models?

Set the `ANTHROPIC_MODEL` environment variable:

```bash
export ANTHROPIC_MODEL=claude-sonnet-4-5-20250929
```

## Troubleshooting

### Authentication failure (Auth Error)

::: warning Common causes
- Token contains extra spaces or is incomplete
- Token has been deleted or regenerated
- Both `API_KEY` and `AUTH_TOKEN` must be set
:::

Fix: Go to the [console](https://fishxcode.com/console/token), verify your token status, and copy-paste it again.

### How do I read error logs?

Go to [Console -> Usage Logs](https://fishxcode.com/console/log), switch the log type to **Error logs**, and filter by time range, model, token, group, request ID, error message, or status code.

::: tip Troubleshooting tips
- Copy the `request_id` from the API response or log entry first, then search for that exact request
- Use the error message filter for keywords in `content`, such as `Invalid token` or `Upstream request failed`
- Use the status code filter to group issues quickly, such as `401`, `429`, `502`, `503`, or `524`
:::

Common error messages:

| Error log content | Meaning | What to do |
|-------------------|---------|------------|
| `status_code=401, Invalid token` | The token is invalid, copied incorrectly, or expired | Copy the token again from the console and remove extra spaces |
| `status_code=429, Account RPM limit exceeded` | The upstream account hit its per-minute request limit | Reduce concurrency and retry frequency, then try again later |
| `status_code=502, Upstream request failed` / `bad response status code 502` | The upstream service or network returned an error | Retry later; if it persists, switch models or contact support with the `request_id` |
| `status_code=502, The origin web server returned an invalid or incomplete response to Cloudflare` | The upstream origin returned an invalid response through Cloudflare | Usually a temporary upstream issue; retry later |
| `status_code=500, upstream error: do request failed` | The request failed while being sent to the upstream service, often due to network or temporary upstream availability issues | Retry later; if it persists, provide the `request_id` to support |
| `status_code=520, bad response status code 520` | Cloudflare returned an unknown error, usually caused by an abnormal upstream response or interrupted connection | Retry later; if it appears frequently, treat it as an upstream incident |
| `status_code=524` / `bad response status code 524` | The upstream response exceeded Cloudflare's 120-second read timeout | Reduce context or output length and avoid long blocking requests |
| `status_code=503, model gpt-image-2 is only supported on /v1/images/generations and /v1/images/edits` | An image model was called through the wrong endpoint | Send image generation/edit requests to the corresponding images endpoint |
| `status_code=500, Image source is a local path that is not readable from this server` | The request contains a local image path that the current upstream cannot read, which may leave terminal input unresponsive | For frontend projects, first check `lock` dependency files: delete the related lock files, or remove abnormal `png` fields from them, then reopen the session; if you still need to send images, use a public `http(s)` image URL or a `data:image/...` base64 payload |

### Request timeout

Possible causes:
1. High network latency
2. Very large input context requiring longer processing
3. Service under heavy load

Try reducing the input context or retry after a short wait.

### Rate limit hit (429)

A `429` status code means you're sending requests too frequently.

::: tip How to handle it
- Slow down and wait a few seconds between requests
- Avoid calling the API in tight loops without delays
- Make sure no other process is using the same token simultaneously
:::

### Model not available

Double-check the model name you specified. Refer to the recommended model list in [Get Started](/en/start). Some models may not yet be available on FishXCode.
