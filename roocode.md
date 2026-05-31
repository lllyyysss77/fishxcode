# 在 RooCode 中使用 FishXCode

## 在 VSCode 中安装 RooCode

1. 在 VSCode 中搜索 [RooCode](https://marketplace.visualstudio.com/items?itemName=RooVeterinaryInc.roo-cline) 并安装
2. 访问 [https://fishxcode.com/console/token](https://fishxcode.com/console/token) 获取你的 API Key

## 新增提供商

完成 RooCode 安装后，打开 RooCode，选择配置提供商，新增一个 OpenAI Compatible 的供应商：

| 配置项 | 值 |
|--------|-----|
| **OpenAI 基础 URL** | `https://api.fishxcode.com/v1` |
| **API 密钥** | 你在 FishXCode 创建的 Key |
| **模型** | `gpt-5` |

::: tip 提示
你也可以将模型修改为 `glm-4.5`、`glm-4.6` 或 `deepseek-v3.1` 等其他模型。
:::

配置完成后，保存即可使用。
