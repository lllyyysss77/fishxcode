---
title: 错误日志说明
description: FishXCode 控制台错误日志字段、状态码含义、常见错误和排障流程。
---

# 错误日志说明

错误日志用于定位单次请求失败的具体原因。排查时建议先查看 [分组健康状态](/group-health) 判断影响范围，再用错误日志里的 `request_id` 定位单条请求。

入口：[控制台 → 使用日志](https://fishxcode.com/console/log)。将日志类型切换为 **错误日志** 后，可按时间、模型、Token、分组、请求 ID、错误消息和状态码筛选。

::: tip 快速判断
- `request_id` 用于定位单条请求
- `status_code` 用于按错误类型聚合
- `content` 用于搜索具体错误关键词
- `group` 用于判断问题是否集中在某个分组
:::

## 字段说明

| 字段 | 含义 | 排查价值 |
|------|------|----------|
| 时间 | 请求失败发生的时间 | 判断问题是否持续发生 |
| 模型 | 请求使用的模型名 | 判断是否只影响某个模型 |
| Token | 请求使用的 API Key 或 Token 名称 | 判断是否只影响某个 Token |
| 分组 | 请求所属套餐、模型或上游资源分组 | 判断是否是分组集中异常 |
| 请求 ID | 单次请求的唯一标识，通常为 `request_id` | 联系支持时优先提供 |
| 错误消息 | 失败原因文本，通常包含 `content` | 用关键词定位具体错误 |
| 状态码 | HTTP 或上游状态码，例如 `401`、`429`、`502` | 用于快速归类错误 |

## 排查顺序

1. 复制接口响应或日志中的 `request_id`，先精确搜索单次请求。
2. 如果没有 `request_id`，按时间范围、模型和 Token 缩小范围。
3. 用状态码聚合错误类型，例如 `401`、`413`、`429`、`502`、`503`。
4. 用错误消息搜索关键词，例如 `Invalid API key`、`daily limit exceeded`、`Upstream request failed`。
5. 如果同一分组集中出现相同错误，进入 [分组健康状态](/group-health) 判断影响范围。

## 问题归因

错误日志可以帮助初步判断问题属于“使用问题”、“上游问题”还是“平台问题”。归因不是只看状态码，还要结合影响范围：

| 归因 | 判断标准 | 典型表现 | 优先处理 |
|------|----------|----------|----------|
| 使用问题 | 只影响单个请求、单个 Token 或特定客户端配置 | 请求体格式错误、参数不匹配、Token 复制错误、上下文过大、接口路径错误 | 检查本地配置、请求体、模型名、Token 和工具版本 |
| 上游问题 | 多个请求集中出现上游失败、网关超时或上游限额 | `Upstream request failed`、`all upstreams failed`、`bad response status code 502/524`、上游账号限流 | 稍后重试，降低并发，必要时切换模型或分组 |
| 平台问题 | 平台节点、资源池或通道不可用 | `system disk overloaded`、`No available accounts`、`No available channel`、平台侧 `Service Unavailable` | 稍后重试；持续出现时联系支持并提供分组、时间范围和 `request_id` |
| 需结合分组判断 | 单条日志无法准确判断责任方 | `429`、`502`、`503` 这类可能由多种原因触发的错误 | 查看 [分组健康状态](/group-health)，确认是否集中发生 |

::: warning 注意
不要只凭一条 `502` 或 `503` 判断责任方。单个请求失败可能是偶发上游抖动；同一分组在同一时间段大量失败，才更适合按上游或平台资源问题处理。
:::

## 状态码分类

| 状态码 | 常见含义 | 初步归因 | 优先处理 |
|--------|----------|----------|----------|
| `400` | 请求体格式、参数或工具消息不符合要求 | 使用问题 | 检查客户端请求体、接口路径和工具 schema |
| `401` | Token 无效、未启用或认证失败 | 使用问题 | 重新复制 Token，确认 Token 状态 |
| `403` | 上游拒绝访问，常见于权限或模型访问限制 | 使用问题或上游问题 | 检查套餐、分组和模型权限 |
| `413` | 请求体过大 | 使用问题 | 缩短上下文，拆分文件，压缩图片 |
| `429` | 频率限制、日额度限制或凭证冷却 | 需结合分组判断 | 降低并发，等待额度或冷却恢复 |
| `500` | 中转层或上游包装后的内部错误 | 需结合错误消息判断 | 结合错误消息判断真实原因 |
| `502` | 上游服务或中间网络异常 | 上游问题或链路问题 | 稍后重试，必要时切换模型/分组 |
| `503` | 服务、通道、账号或平台资源暂不可用 | 上游问题或平台问题 | 稍后重试，持续出现时联系支持 |
| `504` / `521` / `522` / `524` | 网关连接、读取或响应超时 | 上游问题或链路问题 | 减少长任务，稍后重试 |

## 常见错误消息

| 错误日志内容 | 含义 | 初步归因 | 建议处理 |
|-------------|------|----------|----------|
| `status_code=401, Invalid token` | Token 无效、复制错误或已失效 | 使用问题 | 重新复制控制台 Token，确认没有多余空格 |
| `status_code=401, Invalid API key or key is pending admin approval` | API Key 无效，或新 Key 仍在等待审核/启用 | 使用问题或账号状态 | 确认使用的是控制台最新 Token；如果刚创建或更换套餐，等待生效后重试，仍失败请联系支持 |
| `status_code=403, bad response status code 403` | 上游拒绝当前请求，常见于权限、账号状态或模型访问限制 | 使用问题或上游问题 | 检查 Token、套餐分组和模型权限；持续出现时切换模型或联系支持并提供 `request_id` |
| `status_code=413, openai_error` / `bad response status code 413` | 请求体过大，常见于上下文、文件、图片或工具结果过大 | 使用问题 | 缩短上下文，减少一次性上传内容，拆分大文件或压缩图片后重试 |
| `status_code=429, Account RPM limit exceeded` | 上游账号触发每分钟请求限制 | 使用问题或上游限制 | 降低并发和重试频率，稍后再试 |
| `status_code=429, Account daily limit exceeded` | 上游账号触发每日请求上限 | 上游限制 | 等待每日额度重置，或切换其他可用模型/分组 |
| `status_code=429, All credentials for model ... are cooling down` | 当前模型的上游凭证都处于冷却期 | 上游限制 | 降低重试频率，等待冷却结束；紧急任务可临时切换模型 |
| `status_code=500, 请求失败 [429]: {"message":"Too many requests, please wait before trying again."}` | 上游以 `429` 形式返回限流，但被中转层包装为 `500` | 使用问题或上游限制 | 按限流处理：降低并发、延长重试间隔，避免立即连续重试 |
| `status_code=500, auth_unavailable: no auth available` | 当前模型或分组暂时没有可用的上游认证资源 | 平台问题 | 稍后重试；若持续出现，切换可用模型/分组，或联系支持确认套餐和分组状态 |
| `status_code=502, Upstream request failed` / `bad response status code 502` / `all upstreams failed` | 上游服务或中间网络返回异常，或所有可用上游均请求失败 | 上游问题 | 稍后重试；若持续出现，切换模型或联系支持并提供 `request_id` |
| `status_code=502, openai_error` | OpenAI 兼容上游返回异常，但中转层未获得更具体原因 | 上游问题 | 先按上游异常处理；若同一请求稳定复现，减少上下文并提供 `request_id` 给支持 |
| `status_code=502, The origin web server returned an invalid or incomplete response to Cloudflare` | 上游源站经 Cloudflare 返回异常响应 | 上游问题 | 通常为临时上游故障，稍后重试 |
| `status_code=500, upstream error: do request failed` | 请求发送到上游时失败，常见于网络连接或上游临时不可达 | 上游问题或链路问题 | 稍后重试；持续出现时提供 `request_id` 给支持排查 |
| `status_code=520, bad response status code 520` | Cloudflare 返回未知错误，通常表示上游响应异常或连接被中断 | 上游问题或链路问题 | 稍后重试；若集中出现，按上游故障处理 |
| `status_code=521` / `522` / `504` / `524` | Cloudflare 或上游网关连接、读取或响应超时异常 | 上游问题或链路问题 | 先稍后重试；如果只在长任务中出现，减少上下文、输出长度或工具调用链路 |
| `status_code=503, Service Unavailable` | 上游服务暂不可用或当前分组无可用资源 | 需结合分组判断 | 稍后重试；持续出现时切换模型/分组，或联系支持确认资源状态 |
| `status_code=503, system disk overloaded` | 服务节点磁盘水位过高，平台为保护服务临时拒绝请求 | 平台问题 | 这是平台侧容量/节点状态问题，稍后重试；持续出现请联系支持 |
| `status_code=503, No available accounts: no available accounts` | 当前分组没有可用上游账号 | 平台问题 | 切换模型/分组或稍后重试；若长时间存在，联系支持确认资源池 |
| `status_code=503, No available channel for model ... under group ...` | 当前分组下没有该模型的可用通道 | 使用问题或平台问题 | 检查模型名称和套餐支持范围，切换到该分组支持的模型 |
| `status_code=503, model gpt-image-2 is only supported on /v1/images/generations and /v1/images/edits` | 图像模型被用于错误接口 | 使用问题 | 将图像生成/编辑请求发送到对应 images 接口 |
| `status_code=500, Image source is a local path that is not readable from this server` | 请求中包含上游当前无法读取的本地图片路径，可能导致终端输入无响应 | 使用问题 | 前端项目可优先检查 `lock` 系列依赖文件：删除相关锁文件，或移除锁文件中异常的 `png` 字段后重新打开会话；如需继续传图，请改用公网 `http(s)` 图片 URL，或传入 `data:image/...` base64 |
| `status_code=500, failed to parse multipart form` | 图像或文件上传请求体格式不符合接口要求 | 使用问题 | 检查 `multipart/form-data` 字段名、文件字段和请求头；不要手动写错 boundary |
| `status_code=400, Invalid request: prompt is required` / `解析 Images 请求失败: prompt 不能为空` | 图像生成/编辑请求缺少 `prompt` | 使用问题 | 补齐非空 `prompt`，并确认请求发往图像接口 |
| `status_code=400, Unsupported parameter: messages` | 请求接口和参数格式不匹配，常见于把 Chat/Responses 参数发到不支持 `messages` 的接口 | 使用问题 | 检查当前工具的 `base_url`、接口路径和模型类型；按目标接口文档调整请求体 |
| `tool_use ids were found without tool_result blocks immediately after` | 工具调用消息序列不符合 Claude 协议要求 | 使用问题 | 保持每个 `tool_use` 后紧跟对应 `tool_result`；如果由工具自动生成，升级客户端或重新开启会话 |
| `Invalid schema for function ... None is not of type 'array'` | 工具函数 schema 不符合模型要求，通常是 `parameters` 或数组字段为空/类型错误 | 使用问题 | 检查 MCP/工具定义，确保数组字段提供 `[]`，函数参数 schema 符合 JSON Schema |
| `status_code=500, not implemented` | 当前接口、模型能力或工具调用路径暂未实现 | 使用问题或平台问题 | 确认使用的是受支持的接口和模型；必要时切换模型或联系支持确认兼容性 |

## 按类型处理

### 认证与权限

重点看 `401`、`403`、`Invalid API key`、`pending admin approval`、`No available channel`。先确认 Token 是否来自控制台、是否复制完整、是否具备当前模型和分组权限。

### 请求格式

重点看 `400`、`413`、`Unsupported parameter`、`prompt is required`、`Invalid schema`。先检查接口路径、请求体字段、工具 schema、图像请求参数和上下文大小。

### 限流与额度

重点看 `429`、`Max 10/min`、`daily limit exceeded`、`cooling down`。不要无间隔重试；按日志中的限制降低并发，或等待额度/凭证恢复。

### 上游与平台资源

重点看 `502`、`503`、`504`、`520`、`521`、`522`、`524`、`Service Unavailable`、`system disk overloaded`。先稍后重试；若同一分组集中出现，查看 [分组健康状态](/group-health)。

## 联系支持需要的信息

在 `console/log` 的日志列表中，可以从错误信息里一键复制排查内容。联系支持时，建议一次性提供以下信息给技术排查，减少来回确认：

- 用户 ID
- 时间范围：问题开始和最后出现的时间
- 分组名：`group`
- 模型名：请求使用的模型
- 状态码：例如 `429`、`413`、`502`、`503`
- 错误内容：`error_reasons.content`
- 请求 ID：单条日志或接口响应中的 `request_id`
- 影响范围：单个 Token、单个模型、单个分组，还是多个分组同时异常

::: tip 结论
错误日志解释“这一条请求为什么失败”，分组健康状态判断“这个问题是不是集中发生”。两者结合使用，排查效率最高。
:::
