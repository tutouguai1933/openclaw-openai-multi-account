# OpenClaw OpenAI Multi Account

[![Release](https://img.shields.io/github/v/release/tutouguai1933/openclaw-openai-multi-account?sort=semver)](https://github.com/tutouguai1933/openclaw-openai-multi-account/releases)
[![Stars](https://img.shields.io/github/stars/tutouguai1933/openclaw-openai-multi-account?style=social)](https://github.com/tutouguai1933/openclaw-openai-multi-account/stargazers)
[![License: Pending](https://img.shields.io/badge/license-pending-lightgrey)](./LICENSE)

Manage **multiple OpenAI OAuth login accounts inside OpenClaw**, especially multiple `openai-codex` OAuth accounts, with snapshotting, switching, quota inspection, automatic rotation, and fallback handling.

> This repository packages the standalone skill for GitHub distribution and documentation.

## Why this skill exists

When you use OpenClaw with multiple OpenAI OAuth logins, it is easy to lose track of:

- which account is actually active
- which accounts are saved locally
- how much 5-hour / weekly quota is left
- when to switch accounts
- when to fall back to another model

This skill provides a practical operator workflow for managing those accounts safely inside OpenClaw.

## Key capabilities

- Capture the current OpenClaw `openai-codex:default` OAuth profile as a named snapshot
- Add a new OpenAI OAuth account via `openclaw models auth login --provider openai-codex`
- Switch all configured OpenClaw agents to a selected account snapshot
- Detect the **real active account** from OpenClaw auth files
- Inspect cached/observed **5-hour** and **weekly** quota
- Auto-repair stale `ACTIVE` metadata
- Auto-enroll newly logged-in accounts discovered from live OpenClaw auth
- Auto-rotate between same-model accounts near exhaustion
- Fall back to a backup model when all OpenAI accounts are unavailable

## Repository layout

```text
.
├── SKILL.md
├── README.md
└── scripts/
    ├── openclaw-openai-accounts.py
    └── test_openclaw_openai_accounts.py
```

## Quick start

### 1) List current snapshots

```bash
python3 scripts/openclaw-openai-accounts.py list
```

### 2) Probe real quota data

```bash
python3 scripts/openclaw-openai-accounts.py list --verbose --probe
```

### 3) Add a new OpenAI OAuth account

```bash
python3 scripts/openclaw-openai-accounts.py add --name work
```

### 4) Switch account

```bash
python3 scripts/openclaw-openai-accounts.py use work
```

### 5) Auto-pick best account or fallback model

```bash
python3 scripts/openclaw-openai-accounts.py auto
```

## Typical use cases

- “Which OpenAI OAuth account is OpenClaw using right now?”
- “Show all saved local accounts and their quota state.”
- “Switch to another OpenAI account.”
- “Automatically rotate to another account when the current one is nearly exhausted.”
- “Fall back to Bailian when all OpenAI accounts are unavailable.”

## Safety note

This skill may work with sensitive local auth state and cached quota metadata.
Do **not** commit local OAuth tokens or runtime credential files into this repository.

## 中文说明

这个 skill 用于在 **OpenClaw 内部管理多个以 OpenAI OAuth 方式登录的账号**，重点支持多个 `openai-codex` OAuth 账号的统一管理。

它适合以下场景：

- 查看当前真实生效的 OpenAI 账号
- 查看本地保存的全部账号
- 检查 5 小时 / 每周额度
- 在多个同模型账号之间自动切换
- 当 OpenAI 账号都不可用时回退到备用模型

常用命令：

```bash
python3 scripts/openclaw-openai-accounts.py list --verbose
python3 scripts/openclaw-openai-accounts.py use <name>
python3 scripts/openclaw-openai-accounts.py auto
```

## Related

- ClawHub skill: `openclaw-openai-multi-account`
- Intended for OpenClaw environments using multiple OpenAI OAuth logins
