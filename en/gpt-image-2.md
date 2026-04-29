# GPT-Image-2 Image Generation Guide

## Prerequisites

Before you start, prepare the following:

1. A FishXCode account
2. A usable API token
3. Confirm that your current token can access `gpt-image-2`

Get your token here:
- Console: [https://fishxcode.com/console/token](https://fishxcode.com/console/token)

::: tip Tip
If your console shows model groups or permission settings, make sure your current token includes access to `gpt-image-2`. If unsure, create a fresh default token and test with that first.
:::

## Open-source visual tool

If you do not want to write code first, you can use FishXCode's open-source GPT-Image-2 tool to test prompts and generate images directly:

- Live demo: [https://fishxcode-gpt-image-2.lovable.app/](https://fishxcode-gpt-image-2.lovable.app/)
- GitHub repository: [https://github.com/fishxcode/gpt-image-2](https://github.com/fishxcode/gpt-image-2)

::: tip Tip
Use the tool to verify your token, prompt, and image parameters first, then migrate the same parameters into your own code or workflow.
:::

## Method 1: Generate Images via the Images API

This is the most direct approach and maps to the OpenAI-compatible image generation endpoint.

- Endpoint: `https://fishxcode.com/v1/images/generations`
- Model: `gpt-image-2`

### Python Example

```python
import base64
from openai import OpenAI

client = OpenAI(
    api_key="sk-your-fishxcode-token",
    base_url="https://fishxcode.com/v1"
)

result = client.images.generate(
    model="gpt-image-2",
    prompt="An orange cat wearing an astronaut helmet, sitting on the moon, cinematic lighting, ultra-detailed",
    size="1024x1024"
)

image_base64 = result.data[0].b64_json

with open("gpt-image-2-output.png", "wb") as f:
    f.write(base64.b64decode(image_base64))
```

### Node.js Example

```javascript
import fs from "node:fs";
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: "sk-your-fishxcode-token",
  baseURL: "https://fishxcode.com/v1",
});

const result = await client.images.generate({
  model: "gpt-image-2",
  prompt: "A futuristic city floating above the clouds, cyberpunk neon style, ultra-detailed",
  size: "1024x1024",
});

const imageBase64 = result.data[0].b64_json;
fs.writeFileSync("gpt-image-2-output.png", Buffer.from(imageBase64, "base64"));
```

### curl Example

```bash
curl --request POST \
  --url https://fishxcode.com/v1/images/generations \
  --header "Authorization: Bearer sk-your-fishxcode-token" \
  --header "Content-Type: application/json" \
  --data '{
    "model": "gpt-image-2",
    "prompt": "A minimalist product poster on a white background, with a floating transparent glass keyboard in the center, commercial photography style",
    "size": "1024x1024"
  }'
```

::: warning Note
The main result from the Images API is usually `b64_json`, so you need to decode the Base64 string into an actual image file on your side.
:::

## Method 2: Generate Images via Chat Completions

If your workflow is already built around `/v1/chat/completions`, you can also call `gpt-image-2` there.

- Endpoint: `https://fishxcode.com/v1/chat/completions`
- Model: `gpt-image-2`

### Python Example

```python
from openai import OpenAI

client = OpenAI(
    api_key="sk-your-fishxcode-token",
    base_url="https://fishxcode.com/v1"
)

response = client.chat.completions.create(
    model="gpt-image-2",
    messages=[
        {
            "role": "user",
            "content": "Draw a Shiba Inu wearing sunglasses, sitting in a vintage red convertible, road trip style"
        }
    ]
)

print(response)
```

### curl Example

```bash
curl --request POST \
  --url https://fishxcode.com/v1/chat/completions \
  --header "Authorization: Bearer sk-your-fishxcode-token" \
  --header "Content-Type: application/json" \
  --data '{
    "model": "gpt-image-2",
    "messages": [
      {
        "role": "user",
        "content": "Draw a Japanese-style cafe at sunset with warm cinematic composition"
      }
    ]
  }'
```

::: tip Tip
If your goal is simply to get stable image output, prefer `/v1/images/generations`. Use `/v1/chat/completions` only when you need image generation inside an existing chat-based workflow.
:::

## Common Parameters

Commonly used parameters for `gpt-image-2` include:

| Parameter | Meaning | Example |
| --- | --- | --- |
| `model` | Model ID | `gpt-image-2` |
| `prompt` | Image prompt | `a mechanical owl` |
| `size` | Output size | `1024x1024` |
| `n` | Number of images | `1` |
| `background` | Background mode | `transparent` / `white` |
| `quality` | Output quality | `high` / `medium` |

::: warning Note
Parameter compatibility can vary across SDKs and proxy layers. If something fails, reduce to the minimum working set first: `model + prompt + size`.
:::

## Using GPT-Image-2 in Cherry Studio

If you prefer generating images directly in Cherry Studio, configure it like this:

### 1. Add an OpenAI-Compatible Provider

Create a custom provider in Cherry Studio with:

- API Key: your FishXCode token
- Base URL: `https://fishxcode.com/v1`

### 2. Add the Model

Use this model name:

```text
gpt-image-2
```

If Cherry Studio lets you define the model type, choose an image-generation type or an OpenAI Images-compatible type.

### 3. Start Generating

Create a new session or open the image generation panel, select `gpt-image-2`, and enter your prompt.

::: tip Tip
If Cherry Studio does not show image results, check these first:
- The Base URL must be `https://fishxcode.com/v1`
- Your Cherry Studio version must properly support the OpenAI Images API
:::

## FAQ

### Why did the request succeed but no image file appeared?

Because many responses return `b64_json` instead of a saved file. You need to decode that Base64 string and write it into a `.png` file yourself.

### Which endpoint should I prefer?

- Image generation only: prefer `POST /v1/images/generations`
- Need image generation inside an existing chat workflow: consider `POST /v1/chat/completions`

### What should I do if the model does not exist or I have no permission?

Check in this order:

1. Make sure the model name is `gpt-image-2`
2. Make sure the token comes from the [FishXCode Console](https://fishxcode.com/console/token)
3. Make sure your token has access to `gpt-image-2`
4. Make sure the Base URL is `https://fishxcode.com/v1`

### Why do extra parameters sometimes fail?

Because compatibility differs across clients, SDK versions, and proxy layers. Start with the minimum working set and then add optional parameters like `quality`, `background`, and `n` one by one.
