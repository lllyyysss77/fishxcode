# GPT-Image-2 绘图教程

## 前置准备

在开始前，你需要先准备好：

1. 一个 FishXCode 账号
2. 一个可用的 API Token
3. 确认当前 Token 可以访问 `gpt-image-2`

获取 Token：
- 控制台地址：[https://fishxcode.com/console/token](https://fishxcode.com/console/token)

::: tip 提示
如果你在控制台里看得到模型分组或权限配置，请确保当前 Token 覆盖了 `gpt-image-2`。如果不确定，先新建一个默认可用的 Token 进行测试。
:::

## 开源可视化工具

如果你不想先写代码，可以直接使用 FishXCode 提供的开源 GPT-Image-2 工具进行测试和出图：

- 在线体验：[https://fishxcode-gpt-image-2.lovable.app/](https://fishxcode-gpt-image-2.lovable.app/)
- 开源仓库：[https://github.com/fishxcode/gpt-image-2](https://github.com/fishxcode/gpt-image-2)

::: tip 使用建议
先在工具中验证 Token、提示词和图片参数是否可用，再把同样的参数迁移到自己的代码或工作流中。
:::

## 方式一：使用 Images API 生成图片

这是最直接的方式，对应 OpenAI 兼容的图片生成接口。

- 请求地址：`https://api.fishxcode.com/v1/images/generations`
- 模型名称：`gpt-image-2`

### Python 示例

```python
import base64
from openai import OpenAI

client = OpenAI(
    api_key="sk-你的FishXCode-Token",
    base_url="https://api.fishxcode.com/v1"
)

result = client.images.generate(
    model="gpt-image-2",
    prompt="一只戴着宇航员头盔的橘猫，坐在月球表面，电影级光影，超清细节",
    size="1024x1024"
)

image_base64 = result.data[0].b64_json

with open("gpt-image-2-output.png", "wb") as f:
    f.write(base64.b64decode(image_base64))
```

### Node.js 示例

```javascript
import fs from "node:fs";
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: "sk-你的FishXCode-Token",
  baseURL: "https://api.fishxcode.com/v1",
});

const result = await client.images.generate({
  model: "gpt-image-2",
  prompt: "一座漂浮在云海上的未来城市，赛博朋克霓虹风格，超高细节",
  size: "1024x1024",
});

const imageBase64 = result.data[0].b64_json;
fs.writeFileSync("gpt-image-2-output.png", Buffer.from(imageBase64, "base64"));
```

### curl 示例

```bash
curl --request POST \
  --url https://api.fishxcode.com/v1/images/generations \
  --header "Authorization: Bearer sk-你的FishXCode-Token" \
  --header "Content-Type: application/json" \
  --data '{
    "model": "gpt-image-2",
    "prompt": "一张极简产品海报，白色背景，中央是一枚悬浮的透明玻璃键盘，商业摄影风格",
    "size": "1024x1024"
  }'
```

::: warning 注意
Images API 返回的核心结果通常是 `b64_json`，需要你在本地把 Base64 解码成图片文件。
:::

## 方式二：通过 Chat Completions 调用图片生成

如果你的工作流已经基于 `/v1/chat/completions`，也可以直接在对话接口里调用 `gpt-image-2`。

- 请求地址：`https://api.fishxcode.com/v1/chat/completions`
- 模型名称：`gpt-image-2`

### Python 示例

```python
from openai import OpenAI

client = OpenAI(
    api_key="sk-你的FishXCode-Token",
    base_url="https://api.fishxcode.com/v1"
)

response = client.chat.completions.create(
    model="gpt-image-2",
    messages=[
        {
            "role": "user",
            "content": "画一只戴墨镜的柴犬，坐在复古红色敞篷车里，公路旅行风格"
        }
    ]
)

print(response)
```

### curl 示例

```bash
curl --request POST \
  --url https://api.fishxcode.com/v1/chat/completions \
  --header "Authorization: Bearer sk-你的FishXCode-Token" \
  --header "Content-Type: application/json" \
  --data '{
    "model": "gpt-image-2",
    "messages": [
      {
        "role": "user",
        "content": "画一间日落时分的日式咖啡馆，暖色电影感构图"
      }
    ]
  }'
```

::: tip 提示
如果你只是为了“稳定拿到图片结果”，优先用 `/v1/images/generations`。如果你需要把图片生成嵌进现有对话工作流，再考虑 `/v1/chat/completions`。
:::

## 常用参数说明

`gpt-image-2` 常见可用参数如下：

| 参数 | 含义 | 示例 |
| --- | --- | --- |
| `model` | 模型 ID | `gpt-image-2` |
| `prompt` | 绘图提示词 | `一只机械风猫头鹰` |
| `size` | 输出尺寸 | `1024x1024` |
| `n` | 生成张数 | `1` |
| `background` | 背景模式 | `transparent` / `white` |
| `quality` | 输出质量 | `high` / `medium` |

::: warning 注意
不同 SDK 或代理层对参数的兼容程度可能不同。如果你传了某个参数后报错，先退回到最小可运行参数集合：`model + prompt + size`。
:::

## Cherry Studio 中使用 GPT-Image-2

如果你习惯在 Cherry Studio 中直接出图，可以这样配置：

### 1. 新建 OpenAI 兼容提供商

在 Cherry Studio 中添加自定义提供商：

- API Key：你的 FishXCode Token
- Base URL：`https://api.fishxcode.com/v1`

### 2. 添加模型

模型名称填写：

```text
gpt-image-2
```

如果 Cherry Studio 允许配置模型类型，请选择图片生成或兼容 OpenAI Images 的模型类型。

### 3. 开始出图

创建新会话或进入图片生成功能页后，选择 `gpt-image-2`，输入提示词即可生成图片。

::: tip 提示
如果 Cherry Studio 中看不到图片结果，先检查两件事：
- Base URL 是否写成了 `https://api.fishxcode.com/v1`
- 当前版本的 Cherry Studio 是否对 OpenAI 图片接口做了完整适配
:::

## 常见问题

### 为什么返回成功了，却没有看到图片文件？

因为很多接口返回的是 `b64_json`，不是直接落盘文件。你需要自己做一次 Base64 解码并写入 `.png` 文件。

### 应该优先用哪个接口？

- 只做出图：优先 `POST /v1/images/generations`
- 想把图片生成接进既有聊天工作流：再考虑 `POST /v1/chat/completions`

### 报模型不存在或无权限怎么办？

按下面顺序排查：

1. 确认模型名写的是 `gpt-image-2`
2. 确认 Token 来自 [FishXCode 控制台](https://fishxcode.com/console/token)
3. 确认当前 Token 具备 `gpt-image-2` 的访问权限
4. 确认 Base URL 写的是 `https://api.fishxcode.com/v1`

### 为什么参数传多了会报错？

因为不同客户端、不同 SDK 版本、不同代理层的兼容实现并不完全一致。先用最小参数跑通，再逐步加 `quality`、`background`、`n` 之类的可选参数。
