---
title: Group Health
description: FishXCode group health status, field descriptions, error categories, and troubleshooting flow.
---

# Group Health

Group health helps determine whether an API issue is an isolated request failure or a concentrated issue in a plan, model, or upstream group. When troubleshooting API errors, check group health first, then open the single usage log entry to locate the specific `request_id`.

::: info Data scope
The public `status` page embedded here queries group health across all FishXCode users during the selected time window. It reflects platform-wide group availability and is real-time, impartial, and stable.

The **Usage Logs -> Group Health** view inside the console only counts the current user's own requests. Use it to troubleshoot personal tokens, models, and request issues.
:::

<iframe
  src="https://status.fishxcode.com/group/global?view=list&sort=group&window=24h"
  title="FishXCode Group Health"
  loading="lazy"
  referrerpolicy="no-referrer-when-downgrade"
  style="width: 100%; height: 720px; border: 1px solid var(--vp-c-divider); border-radius: 8px; background: var(--vp-c-bg-soft);"
></iframe>

If the status page above does not load correctly, open [FishXCode Group Health](https://status.fishxcode.com/group/global?view=list&sort=group&window=24h) directly.

Console entry: [Console -> Usage Logs](https://fishxcode.com/console/log). In error logs or statistics views, filter by time range, model, token, group, error message, and status code.

## Console Example

The screenshot below shows **Usage Logs -> Group Health** in the console. The time range is April 6, 2026 to May 1, 2026, showing the global group's success rate, request count, average latency, most recent request time, and failure reasons.

![Console group health example](/img/group-health.png)

::: tip Usage principle
Identify the impact scope first, then handle the single error. A single log entry is best for locating one request; group health is best for determining whether the issue is concentrated.
:::

For single error message explanations, see [Error Logs](/en/error-logs).

## Fields

| Field | Meaning | Troubleshooting value |
|-------|---------|-----------------------|
| `group` | Request group, such as a plan group, default group, or specific model group | Check whether the issue is concentrated in one plan, model, or upstream resource pool |
| `total_count` | Total request count in the current time range | Check whether the sample size is large enough and avoid being misled by a few requests |
| `success_count` | Successful request count | Compare with `total_count` to understand overall availability |
| `error_count` | Failed request count | When errors keep rising, check `error_reasons` first |
| `success_rate` | Success rate | A clearly low success rate usually means the group has a concentrated issue |
| `avg_use_time` | Average latency in seconds | When latency rises, focus on long context, long output, tool chains, and slow upstream responses |
| `quota` | Group quota or quota statistics value | Combine with plan and console balance to check whether quota limits are close |
| `tokens` | Token consumption in the current time range | Check for abnormal consumption or large-context requests |
| `first_seen_at` | First seen time in the statistics window | Locate when the issue started |
| `last_seen_at` | Last seen time in the statistics window | Check whether the issue is still ongoing |
| `error_reasons` | High-frequency error reasons and counts | Handle the most frequent error first; do not rely only on the latest log entry |

## Troubleshooting Flow

### 1. Check success rate and error count first

If `success_rate` is close to normal and `error_count` is low, it is usually a transient error. Copy the `request_id` from the single request and continue troubleshooting.

If one group's `success_rate` is clearly lower than other groups, or `error_count` is concentrated, prioritize group-level checks for model, token, upstream account, plan permissions, and platform resource status.

### 2. Check top error reasons

`error_reasons` is usually sorted by occurrence count. Start with the most frequent error, then inspect lower-frequency errors. High-frequency errors show the main failure type in the current time range.

| Error type | Common log keywords | Initial attribution | What to check first |
|------------|---------------------|---------------------|---------------------|
| Rate limit | `Account RPM limit exceeded`, `Max 10/min`, `Max 5/min` | Usage issue or upstream limit | Concurrency or requests per minute are too high |
| Daily quota limit | `Account daily limit exceeded` | Upstream limit | Upstream account daily quota is exhausted |
| Credential cooldown | `All credentials ... are cooling down` | Upstream limit | All upstream credentials for the current model are cooling down |
| Request body too large | `status_code=413`, `openai_error` | Usage issue | Context, files, images, or tool results are too large |
| Permission or authentication | `401`, `403`, `Invalid API key`, `pending admin approval` | Usage issue or account status | Token, plan, group, or model permissions are abnormal |
| No available resource | `No available accounts`, `No available channel`, `auth_unavailable` | Platform issue or configuration issue | Current group has no available account, channel, or authentication resource |
| Upstream error | `502`, `all upstreams failed`, `Upstream request failed` | Upstream issue | Upstream service or intermediate network is abnormal |
| Gateway timeout | `504`, `521`, `522`, `524` | Upstream or network issue | Upstream connection, read, or response timed out |
| Platform resource protection | `system disk overloaded`, `Service Unavailable` | Platform issue | Platform node or upstream resource is temporarily unavailable |
| Image endpoint format | `gpt-image-2`, `prompt is required`, `multipart form` | Usage issue | Image endpoint path, prompt, or upload format is wrong |
| Tool-call format | `tool_use`, `tool_result`, `Invalid schema` | Usage issue | Client tool messages or JSON Schema do not meet requirements |

### 3. Handle by impact scope

| Symptom | More likely cause | What to do |
|---------|-------------------|------------|
| Only one token fails | Token configuration, permission, or local request format issue | Copy the token again and check client configuration and request body |
| Only one model fails | Model permission, model channel, or upstream model resource issue | Switch to a similar model and confirm the current plan supports the model |
| Only one group has a low success rate | Group resource pool, plan permission, or upstream account issue | Switch group/model; when contacting support, provide the group name and time range |
| Multiple groups show `502`, `504`, `521`, `522`, or `524` at the same time | Upstream or network path issue | Retry later and reduce long-running tasks; contact support if it persists |
| Multiple requests show `413` | Request body is too large | Shorten context, split files, compress images, or reduce tool results |
| Multiple requests show `429` | Request rate is too high, daily quota is exhausted, or credentials are cooling down | Reduce concurrency; distinguish RPM, daily limit, and cooldown from the log |

## Information for Support

For simple issues, check [Error Logs](/en/error-logs) and [Group Health](/en/group-health) first. If the issue remains unresolved, open the error log details in `console/log` and click the copy icon to copy the troubleshooting details in one click. When contacting support, provide the following in one message so the technical team can investigate with less back-and-forth:

- User ID
- Time range: when the issue started and when it last appeared
- Group name: `group`
- Model name used by the request
- Status code, such as `429`, `413`, `502`, or `503`
- Error content: `error_reasons.content`
- Request ID: `request_id` from a single log entry or API response
- Impact scope: one token, one model, one group, or multiple groups at the same time

::: tip Quick summary
For `401` / `403`, check permissions. For `413`, check request body size. For `429`, check rate and quota. For `502` / `504` / `524`, check upstream and long-running tasks. For `503`, check whether resources are temporarily unavailable.
:::
