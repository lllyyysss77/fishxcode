# Using FishXCode with RooCode

## Install RooCode in VSCode

1. Search for [RooCode](https://marketplace.visualstudio.com/items?itemName=RooVeterinaryInc.roo-cline) in VSCode and install it
2. Visit [https://fishxcode.com/console/token](https://fishxcode.com/console/token) to get your API Key

## Add Provider

After installing RooCode, open it and select configure provider. Add an OpenAI Compatible provider:

| Setting | Value |
|---------|-------|
| **OpenAI Base URL** | `https://api.fishxcode.com/v1` |
| **API Key** | Your key from FishXCode |
| **Model** | `gpt-5` |

::: tip
You can also use other models like `glm-4.5`, `glm-4.6`, or `deepseek-v3.1`.
:::

Save your configuration and start using it.
