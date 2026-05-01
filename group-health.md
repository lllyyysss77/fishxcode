---
title: 分组健康状态
description: FishXCode 分组健康状态、字段说明、错误分类和排障流程。
---

# 分组健康状态

分组健康状态用于判断调用异常是个别请求问题，还是某个套餐、模型或上游分组的集中问题。排查接口异常时，先看分组健康状态，再进入单条使用日志定位具体 `request_id`。

::: info 数据口径
本页嵌入的公开 `status` 页面查询的是所选时间段内 FishXCode 整个平台所有用户正在使用的分组健康状态，反映全平台分组可用性，具备实时性、公正性和稳定性。

控制台内的 **使用日志 → 分组健康状态** 只统计当前用户自己的调用数据，适合排查个人 Token、模型和请求问题。
:::

<iframe
  src="https://status.fishxcode.com/group/global?view=list&sort=group&window=24h"
  title="FishXCode 分组健康状态"
  loading="lazy"
  referrerpolicy="no-referrer-when-downgrade"
  style="width: 100%; height: 720px; border: 1px solid var(--vp-c-divider); border-radius: 8px; background: var(--vp-c-bg-soft);"
></iframe>

如果上方状态页未正常加载，可直接打开 [FishXCode 分组健康状态](https://status.fishxcode.com/group/global?view=list&sort=group&window=24h)。

控制台入口：[控制台 → 使用日志](https://fishxcode.com/console/log)。在错误日志或统计视图中，按时间范围、模型、Token、分组、错误消息和状态码筛选。

## 控制台示例

下面是控制台 **使用日志 → 分组健康状态** 的示例截图，时间范围为 2026-04-06 至 2026-05-01，展示全局分组的成功率、请求数、平均耗时、最近请求时间和失败原因。

![控制台分组健康状态示例](/img/group-health.png)

::: tip 使用原则
先判断影响范围，再处理单条错误。单条日志适合定位具体请求，分组健康状态适合判断问题是否集中发生。
:::

需要解释单条错误消息时，请查看 [错误日志说明](/error-logs)。

## 字段说明

| 字段 | 含义 | 排查价值 |
|------|------|----------|
| `group` | 请求所属分组，例如套餐分组、默认分组或特定模型分组 | 判断问题是否集中在某个套餐、模型或上游资源池 |
| `total_count` | 当前时间范围内的总请求数 | 判断样本量是否足够，避免被少量请求误导 |
| `success_count` | 成功请求数 | 与 `total_count` 对比可判断整体可用性 |
| `error_count` | 失败请求数 | 错误数持续升高时，优先查看 `error_reasons` |
| `success_rate` | 成功率 | 成功率明显偏低时，通常说明该分组存在集中问题 |
| `avg_use_time` | 平均耗时，单位为秒 | 耗时升高时，重点排查长上下文、长输出、工具链路和上游响应慢 |
| `quota` | 分组额度或额度统计值 | 结合套餐和控制台余额判断是否接近额度限制 |
| `tokens` | 当前时间范围内的 Token 消耗 | 用于判断是否存在异常消耗或大上下文请求 |
| `first_seen_at` | 统计周期内首次出现时间 | 用于定位问题开始时间 |
| `last_seen_at` | 统计周期内最后一次出现时间 | 用于判断问题是否仍在持续 |
| `error_reasons` | 高频错误原因及次数 | 优先处理次数最多的错误，不要只看最后一条日志 |

## 排查流程

### 一、先看成功率和错误数

如果 `success_rate` 接近正常水平，且 `error_count` 很低，通常是偶发错误，可复制单条请求的 `request_id` 继续排查。

如果某个分组的 `success_rate` 明显低于其他分组，或 `error_count` 集中升高，优先按分组排查模型、Token、上游账号、套餐权限和平台资源状态。

### 二、再看 Top 错误原因

`error_reasons` 通常按出现次数展示。排查时先处理最高频错误，再看低频错误。高频错误能反映当前时间范围内的主要故障类型。

| 错误类型 | 常见日志关键词 | 初步归因 | 优先判断 |
|----------|----------------|----------|----------|
| 频率限制 | `Account RPM limit exceeded`、`Max 10/min`、`Max 5/min` | 使用问题或上游限制 | 并发或每分钟请求数过高 |
| 日额度限制 | `Account daily limit exceeded` | 上游限制 | 上游账号今日额度已用尽 |
| 凭证冷却 | `All credentials ... are cooling down` | 上游限制 | 当前模型上游凭证均处于冷却期 |
| 请求体过大 | `status_code=413`、`openai_error` | 使用问题 | 上下文、文件、图片或工具结果过大 |
| 权限或认证 | `401`、`403`、`Invalid API key`、`pending admin approval` | 使用问题或账号状态 | Token、套餐、分组或模型权限异常 |
| 无可用资源 | `No available accounts`、`No available channel`、`auth_unavailable` | 平台问题或配置问题 | 当前分组没有可用账号、通道或认证资源 |
| 上游异常 | `502`、`all upstreams failed`、`Upstream request failed` | 上游问题 | 上游服务或中间网络异常 |
| 网关超时 | `504`、`521`、`522`、`524` | 上游问题或链路问题 | 上游连接、读取或响应超时 |
| 平台资源保护 | `system disk overloaded`、`Service Unavailable` | 平台问题 | 平台节点或上游资源暂不可用 |
| 图像接口格式 | `gpt-image-2`、`prompt is required`、`multipart form` | 使用问题 | 图像接口路径、提示词或上传格式错误 |
| 工具调用格式 | `tool_use`、`tool_result`、`Invalid schema` | 使用问题 | 客户端工具消息或 JSON Schema 不符合要求 |

### 三、按影响范围处理

| 现象 | 更可能的原因 | 建议处理 |
|------|--------------|----------|
| 只有单个 Token 报错 | Token 配置、权限或本地请求格式问题 | 重新复制 Token，检查客户端配置和请求体 |
| 只有单个模型报错 | 模型权限、模型通道或上游模型资源问题 | 切换同类模型，确认当前套餐是否支持该模型 |
| 只有单个分组成功率低 | 分组资源池、套餐权限或上游账号问题 | 切换分组/模型，联系支持时提供分组名和时间范围 |
| 多个分组同时出现 `502`、`504`、`521`、`522`、`524` | 上游或网络链路异常 | 稍后重试，减少长任务；持续出现时联系支持 |
| 多个请求都出现 `413` | 请求体过大 | 缩短上下文，拆分文件，压缩图片或减少工具结果 |
| 多个请求都出现 `429` | 请求频率过高、日额度用尽或凭证冷却 | 降低并发；根据日志区分 RPM、daily limit 和 cooling down |

## 支持排查需要的信息

遇到简单问题时，建议先查看 [错误日志说明](/error-logs) 和 [分组健康状态说明](/group-health) 自查。仍无法解决时，可以在 `console/log` 的错误日志详情中点击复制图标，一键复制排查内容。联系支持时，建议一次性提供以下信息给技术排查，减少来回确认：

- 用户 ID
- 时间范围：问题开始和最后出现的时间
- 分组名：`group`
- 模型名：请求使用的模型
- 状态码：例如 `429`、`413`、`502`、`503`
- 错误内容：`error_reasons.content`
- 请求 ID：单条日志或接口响应中的 `request_id`
- 影响范围：单个 Token、单个模型、单个分组，还是多个分组同时异常

::: tip 快速结论
`401` / `403` 多看权限，`413` 多看请求体，`429` 多看频率和额度，`502` / `504` / `524` 多看上游和长任务，`503` 多看资源是否暂不可用。
:::
