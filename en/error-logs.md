---
title: Error Logs
description: FishXCode console error log fields, status codes, common errors, and troubleshooting flow.
---

# Error Logs

Error logs help identify why a specific request failed. When troubleshooting, check [Group Health](/en/group-health) first to understand the impact scope, then use the `request_id` in the error log to locate the exact request.

Entry: [Console -> Usage Logs](https://fishxcode.com/console/log). Switch the log type to **Error logs**, then filter by time range, model, token, group, request ID, error message, or status code.

::: tip Quick checks
- `request_id` locates one specific request
- `status_code` groups errors by type
- `content` searches for specific error keywords
- `group` shows whether failures are concentrated in one group
:::

## Fields

| Field | Meaning | Troubleshooting value |
|-------|---------|-----------------------|
| Time | When the request failed | Check whether the issue is ongoing |
| Model | Model used by the request | Check whether only one model is affected |
| Token | API key or token name used by the request | Check whether only one token is affected |
| Group | Plan, model, or upstream resource group for the request | Check whether failures are concentrated in one group |
| Request ID | Unique identifier for one request, usually `request_id` | Provide this first when contacting support |
| Error message | Failure reason text, usually containing `content` | Search by keyword to find the exact error |
| Status code | HTTP or upstream status code, such as `401`, `429`, or `502` | Classify the error quickly |

## Troubleshooting Order

1. Copy the `request_id` from the API response or log entry and search for that exact request first.
2. If there is no `request_id`, narrow the scope by time range, model, and token.
3. Group errors by status code, such as `401`, `413`, `429`, `502`, or `503`.
4. Search error message keywords, such as `Invalid API key`, `daily limit exceeded`, or `Upstream request failed`.
5. If the same error is concentrated in one group, open [Group Health](/en/group-health) to confirm the impact scope.

## Attribution

Error logs help determine whether an issue is caused by usage, upstream services, or the platform. Attribution should not rely on status code alone; combine it with the impact scope.

| Attribution | How to identify it | Typical signs | First action |
|-------------|--------------------|---------------|--------------|
| Usage issue | Affects only one request, one token, or a specific client configuration | Invalid request body, parameter mismatch, copied token error, oversized context, wrong endpoint path | Check local configuration, request body, model name, token, and tool version |
| Upstream issue | Multiple requests show upstream failures, gateway timeouts, or upstream quota limits | `Upstream request failed`, `all upstreams failed`, `bad response status code 502/524`, upstream account rate limits | Retry later, reduce concurrency, or switch model/group if needed |
| Platform issue | Platform nodes, resource pools, or channels are unavailable | `system disk overloaded`, `No available accounts`, `No available channel`, platform-side `Service Unavailable` | Retry later; if it persists, contact support with the group, time range, and `request_id` |
| Needs group context | One log entry is not enough to identify responsibility | Errors such as `429`, `502`, or `503` can have multiple causes | Check [Group Health](/en/group-health) to confirm whether failures are concentrated |

::: warning Note
Do not assign responsibility from a single `502` or `503`. One failed request may be a transient upstream fluctuation. A large number of failures in the same group and time window is a better signal for upstream or platform resource issues.
:::

## Status Code Categories

| Status code | Common meaning | Initial attribution | First action |
|-------------|----------------|---------------------|--------------|
| `400` | Request body, parameters, or tool messages are invalid | Usage issue | Check the client request body, endpoint path, and tool schema |
| `401` | Token is invalid, disabled, or failed authentication | Usage issue | Copy the token again and confirm its status |
| `403` | Upstream rejected access, often due to permission or model access limits | Usage or upstream issue | Check plan, group, and model permissions |
| `413` | Request body is too large | Usage issue | Shorten context, split files, or compress images |
| `429` | Rate limit, daily quota limit, or credential cooldown | Needs group context | Reduce concurrency and wait for quota or cooldown recovery |
| `500` | Internal error from relay or wrapped upstream error | Depends on error message | Read the error message for the real cause |
| `502` | Upstream service or intermediate network error | Upstream or network issue | Retry later; switch model/group if needed |
| `503` | Service, channel, account, or platform resource is temporarily unavailable | Upstream or platform issue | Retry later; contact support if it persists |
| `504` / `521` / `522` / `524` | Gateway connection, read, or response timeout | Upstream or network issue | Reduce long-running requests and retry later |

## Common Error Messages

| Error log content | Meaning | Initial attribution | What to do |
|-------------------|---------|---------------------|------------|
| `status_code=401, Invalid token` | Token is invalid, copied incorrectly, or expired | Usage issue | Copy the console token again and remove extra spaces |
| `status_code=401, Invalid API key or key is pending admin approval` | API key is invalid, or a new key is still waiting for approval/activation | Usage issue or account status | Confirm you are using the latest console token; if it was just created or the plan changed, wait for it to take effect and retry. Contact support if it still fails |
| `status_code=403, bad response status code 403` | Upstream rejected the request, commonly due to permission, account status, or model access limits | Usage or upstream issue | Check token, plan group, and model permissions; if it persists, switch models or contact support with the `request_id` |
| `status_code=413, openai_error` / `bad response status code 413` | Request body is too large, often due to context, files, images, or tool results | Usage issue | Shorten context, reduce one-time uploads, split large files, or compress images before retrying |
| `status_code=429, Account RPM limit exceeded` | The upstream account hit its per-minute request limit | Usage issue or upstream limit | Reduce concurrency and retry frequency, then try again later |
| `status_code=429, Account daily limit exceeded` | The upstream account hit its daily request limit | Upstream limit | Wait for daily quota reset, or switch to another available model/group |
| `status_code=429, All credentials for model ... are cooling down` | All upstream credentials for the current model are cooling down | Upstream limit | Reduce retry frequency and wait for cooldown; switch models temporarily for urgent tasks |
| `status_code=500, 请求失败 [429]: {"message":"Too many requests, please wait before trying again."}` | Upstream returned a `429` rate limit, but the relay wrapped it as `500` | Usage issue or upstream limit | Treat it as rate limiting: reduce concurrency, increase retry intervals, and avoid immediate repeated retries |
| `status_code=500, auth_unavailable: no auth available` | No upstream authentication resource is currently available for the model or group | Platform issue | Retry later; if it persists, switch to an available model/group or contact support to confirm plan and group status |
| `status_code=502, Upstream request failed` / `bad response status code 502` / `all upstreams failed` | Upstream service or intermediate network returned an error, or all available upstreams failed | Upstream issue | Retry later; if it persists, switch models or contact support with the `request_id` |
| `status_code=502, openai_error` | An OpenAI-compatible upstream returned an error, but the relay did not receive a more specific reason | Upstream issue | Treat it as an upstream error first; if the same request reproduces consistently, reduce context and provide the `request_id` to support |
| `status_code=502, The origin web server returned an invalid or incomplete response to Cloudflare` | The upstream origin returned an invalid response through Cloudflare | Upstream issue | Usually a temporary upstream failure; retry later |
| `status_code=500, upstream error: do request failed` | The request failed while being sent upstream, often due to network connectivity or temporary upstream unavailability | Upstream or network issue | Retry later; if it persists, provide the `request_id` to support |
| `status_code=520, bad response status code 520` | Cloudflare returned an unknown error, usually due to an abnormal upstream response or interrupted connection | Upstream or network issue | Retry later; if concentrated, treat it as an upstream incident |
| `status_code=521` / `522` / `504` / `524` | Cloudflare or upstream gateway connection, read, or response timeout | Upstream or network issue | Retry later first; for long-running tasks, reduce context, output length, or tool-call chains |
| `status_code=503, Service Unavailable` | Upstream service is temporarily unavailable, or the current group has no available resources | Needs group context | Retry later; if it persists, switch model/group or contact support to confirm resource status |
| `status_code=503, system disk overloaded` | A service node has high disk usage, so the platform temporarily rejects requests to protect service stability | Platform issue | This is platform-side capacity or node state; retry later and contact support if it persists |
| `status_code=503, No available accounts: no available accounts` | The current group has no available upstream account | Platform issue | Switch model/group or retry later; if it lasts for a long time, contact support to confirm the resource pool |
| `status_code=503, No available channel for model ... under group ...` | There is no available channel for this model under the current group | Usage or platform issue | Check the model name and plan support scope, then switch to a model supported by this group |
| `status_code=503, model gpt-image-2 is only supported on /v1/images/generations and /v1/images/edits` | An image model was called through the wrong endpoint | Usage issue | Send image generation/edit requests to the corresponding images endpoint |
| `status_code=500, Image source is a local path that is not readable from this server` | The request contains a local image path that the upstream cannot read, which may leave terminal input unresponsive | Usage issue | For frontend projects, first check `lock` dependency files: delete related lock files, or remove abnormal `png` fields from them and reopen the session. To keep sending images, use a public `http(s)` image URL or a `data:image/...` base64 payload |
| `status_code=500, failed to parse multipart form` | The image or file upload request body does not match the API format | Usage issue | Check `multipart/form-data` field names, file fields, and request headers; do not write an invalid boundary manually |
| `status_code=400, Invalid request: prompt is required` / `解析 Images 请求失败: prompt 不能为空` | Image generation/edit request is missing `prompt` | Usage issue | Provide a non-empty `prompt` and confirm the request is sent to an image endpoint |
| `status_code=400, Unsupported parameter: messages` | Endpoint and parameter format do not match, commonly when Chat/Responses parameters are sent to an endpoint that does not support `messages` | Usage issue | Check the tool `base_url`, endpoint path, and model type; adjust the request body for the target API |
| `tool_use ids were found without tool_result blocks immediately after` | Tool-call message sequence does not meet the Claude protocol requirements | Usage issue | Keep each `tool_use` immediately followed by its corresponding `tool_result`; if generated by a tool, upgrade the client or start a new session |
| `Invalid schema for function ... None is not of type 'array'` | Tool function schema is invalid, usually because `parameters` or an array field is empty or has the wrong type | Usage issue | Check MCP/tool definitions, provide `[]` for array fields, and ensure function parameter schemas follow JSON Schema |
| `status_code=500, not implemented` | The current endpoint, model capability, or tool-call path is not implemented | Usage or platform issue | Confirm you are using a supported endpoint and model; switch models or contact support if needed |

## Handle by Type

### Authentication and permissions

Focus on `401`, `403`, `Invalid API key`, `pending admin approval`, and `No available channel`. First confirm that the token comes from the console, was copied completely, and has permission for the current model and group.

### Request format

Focus on `400`, `413`, `Unsupported parameter`, `prompt is required`, and `Invalid schema`. First check the endpoint path, request body fields, tool schema, image request parameters, and context size.

### Rate limits and quota

Focus on `429`, `Max 10/min`, `daily limit exceeded`, and `cooling down`. Do not retry with no delay. Reduce concurrency according to the log message, or wait for quota/credential recovery.

### Upstream and platform resources

Focus on `502`, `503`, `504`, `520`, `521`, `522`, `524`, `Service Unavailable`, and `system disk overloaded`. Retry later first; if errors are concentrated in the same group, check [Group Health](/en/group-health).

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

::: tip Summary
Error logs explain why one request failed. Group health shows whether the issue is concentrated. Use both together for faster troubleshooting.
:::
