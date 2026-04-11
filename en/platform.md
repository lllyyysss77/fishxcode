---
title: Platform Navigation & System Guide
description: FishXCode platform architecture, console features, page routes, and documentation reference.
---

# Platform Navigation & System Guide

> This page provides an overview of the FishXCode platform structure to help you quickly locate features.
> Need to create an account? See [Account Setup & Management](/en/account).

## 1. Platform Overview

FishXCode is an AI access platform for developers and teams -- a unified gateway with website, pricing, and docs on the front end, and console, tokens, billing, invoices, and usage logs on the back end.

The platform supports mainstream AI models. Specific channels and models are subject to change -- check the [Pricing page](https://fishxcode.com/pricing) for details. From registration to token creation, plan selection, tool integration, and usage tracking, everything is handled in one system.

---

## 2. Site Module Overview

The entire system is composed of 4 major modules:

| Route | Page | Module | Description |
|---|---|---|---|
| `/` | Homepage | Website | Introduces platform capabilities, displays announcements, guides users to register or view docs |
| `/pricing` | Pricing | Website | View model pricing, plans, and consumption rates for different groups |
| `/login` | Login | Auth Center | Username/email login, with GitHub and LinuxDO third-party login support |
| `/register` | Register | Auth Center | New user registration entry |
| `/console` | Dashboard | Console | Overview: balance, usage stats, request count, and system notifications |
| `/console/package` | Package Management | Console | View and purchase subscription plans, see covered models |
| `/console/token` | Token Management | Console | Create, copy, and delete API Keys; manage groups and expiration |
| `/console/log` | Usage Logs | Console | View call time, model, consumption, and detailed records for each request |
| `/console/topup` | Top-Up | Console | Balance top-up, redeem codes, and top-up history |
| `/console/invoice` | Invoice Management | Console | Submit invoicing requests and track invoice status |
| `/console/invite` | Referral Rewards | Console | Referral links, reward rules, earnings, and leaderboard |
| `/console/personal` | Personal Settings | Console | Account info, default group, check-in rewards |
| `/status` | Service Status | Public Page | Check whether each group's service is operating normally |
| `/contact` | Contact Us | Public Page | Official contact channels and after-sales support |
| `/docs` | Documentation | Public Page | Integration tutorials, tool guides, and FAQ |

---

## 3. Console Quick Reference

After logging in, all operations are centralized in the console. Use the following sections to quickly locate what you need:

### Getting Started

1. Register an account -> Log in to the console
2. **Token Management**: Create an API Key (select the appropriate group)
3. Configure the Base URL and API Key in your tool

### Purchase & Top-Up

- **Package Management**: View available plans and subscribe to the one that fits your needs
- **Top-Up Page**: Supports Alipay, WeChat Pay, and redeem codes
- **Balance Check**: Displayed in real time on the Dashboard homepage

### Usage Management

- **Usage Logs**: View records for each API call, including model, consumption, IP, and other details
- **Token Management**: Check token status and group scope, delete tokens that are no longer in use
- **Personal Settings**: Change your default group, view check-in rewards and basic statistics

### Support & After-Sales

- **Invoice Management**: Submit invoicing requests for enterprise or reimbursement needs
- **Referral Rewards**: Invite new users via referral links and track reward earnings
- **Service Status**: Check whether each group's service is running normally when troubleshooting call issues
- **Contact Us**: Handle usage issues, procurement inquiries, and after-sales support

---

## 4. Documentation Reference

`/docs` is the FishXCode documentation center. The content maps directly to the pages on this documentation site:

| Doc Page | Route | Content |
|---|---|---|
| Homepage | `/` | Documentation homepage and product overview |
| Quick Start | `/start` | Claude Code integration tutorial |
| Account Setup | `/account` | Registration, login, tokens, top-up, and account management |
| ZCF Quick Start | `/zcf` | ZCF workflow and MCP configuration |
| OpenAI Codex | `/codex` | Codex integration guide |
| Cursor | `/cursor` | Cursor integration guide |
| RooCode | `/roocode` | RooCode integration guide |
| Qwen Code | `/qwencode` | Qwen Code integration guide |
| Droid CLI | `/droid` | Droid CLI integration guide |
| OpenCode | `/opencode` | OpenCode integration guide |
| OpenClaw | `/openclaw` | OpenClaw integration guide |
| Tool Comparison | `/compare` | Multi-tool comparison guide |
| Supported Models | `/models` | Model list and capability overview |
| FAQ | `/faq` | Frequently asked questions and troubleshooting |
| Changelog | `/changelog` | Version update records |
| Terms of Service | `/terms` | Platform terms and conditions |
| Privacy Policy | `/privacy` | Privacy notice |

::: tip In a Nutshell
Users discover the platform through the website, register and activate via the console, manage tokens/plans/billing/logs for daily use, with docs and status pages providing guidance and assurance.
:::
