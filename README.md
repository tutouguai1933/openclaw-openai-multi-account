# OpenClaw OpenAI Multi Account

[![Release](https://img.shields.io/github/v/release/tutouguai1933/openclaw-openai-multi-account?sort=semver)](https://github.com/tutouguai1933/openclaw-openai-multi-account/releases)
[![Stars](https://img.shields.io/github/stars/tutouguai1933/openclaw-openai-multi-account?style=social)](https://github.com/tutouguai1933/openclaw-openai-multi-account/stargazers)
[![License: MIT](https://img.shields.io/badge/license-MIT-green.svg)](./LICENSE)

Manage **multiple OpenAI OAuth login accounts inside OpenClaw**, especially multiple `openai-codex` accounts, with:

- real active-account detection
- snapshot capture and switching
- 5-hour + weekly quota inspection
- automatic rotation near exhaustion
- session-safe switching rules
- fallback model handling
- auth alias/config reconciliation

> This repository packages the standalone skill for GitHub distribution and documentation.

---

## What this skill solves

When you run OpenClaw with multiple OpenAI OAuth accounts, common pain points are:

- not knowing which account is **actually live** right now
- losing track of which accounts are saved locally
- stale alias/config metadata after manual re-login
- switching too late and hitting 5-hour rate limits
- switching too early and interrupting active conversations

This skill makes that workflow predictable and automatable.

---

## Key capabilities

- Capture the current OpenClaw `openai-codex:default` profile as a named snapshot
- Add a new OpenAI OAuth account via official `openclaw models auth login --provider openai-codex`
- Switch all configured OpenClaw agents to one selected account snapshot
- Detect the **real live account** from agent auth files
- Inspect cached/observed **5-hour** and **weekly** quota
- Treat **email as canonical account identity** for dedupe and reconciliation
- Auto-enroll newly discovered live logins into the saved account list
- Keep `openclaw.json` and agent `auth-profiles.json` aliases in sync
- Prune stale aliases that no longer correspond to saved accounts
- Auto-rotate accounts with **soft + hard thresholds**
- Protect active sessions from unnecessary switching
- Fall back to a backup model when all OpenAI accounts are unsuitable

---

## Strategy at a glance

### Default unattended policy

- **Check cadence**: every **10 minutes**
- **Inactivity guard**: require **3 minutes** of no non-cron session activity for soft switches
- **5-hour soft threshold**: switch attempt at **80% used** (about 20% left)
- **5-hour hard threshold**: switch immediately at **90% used** (about 10% left)
- **Weekly soft threshold**: switch attempt at **90% used**
- **Weekly hard threshold**: switch immediately at **95% used**
- **Fallback model**: `bailian/qwen3.5-plus`

### Full policy table

| Signal | Threshold | Action |
|---|---:|---|
| 5-hour usage | `< 80%` | Keep current account |
| 5-hour usage | `>= 80% and < 90%` | Try switching **only if** all non-cron sessions have been inactive for 3 minutes |
| 5-hour usage | `>= 90%` | **Immediate switch**, even if sessions are active |
| Weekly usage | `< 90%` | Keep current account |
| Weekly usage | `>= 90% and < 95%` | Try switching **only if** all non-cron sessions have been inactive for 3 minutes |
| Weekly usage | `>= 95%` | **Immediate switch**, even if sessions are active |
| No healthy OpenAI account available | N/A | Switch to fallback model |

### Selection behavior

When a switch is needed, the script will:

1. prefer another healthy OpenAI account for the **same primary model**
2. keep auth aliases/config in sync while switching
3. fall back to the backup model only if no suitable OpenAI account remains

---

## Command examples

### 1) List all saved accounts

```bash
python3 scripts/openclaw-openai-accounts.py list
```

### 2) Probe real quota and health

```bash
python3 scripts/openclaw-openai-accounts.py list --verbose --probe
```

### 3) Show current live account status

```bash
python3 scripts/openclaw-openai-accounts.py status --probe
```

### 4) Add a new account with official OpenClaw OAuth login

```bash
python3 scripts/openclaw-openai-accounts.py add --name work
```

### 5) Capture current live login into a named snapshot

```bash
python3 scripts/openclaw-openai-accounts.py capture account4
```

### 6) Switch to a specific account

```bash
python3 scripts/openclaw-openai-accounts.py use account2
```

### 7) Run one auto-rotation decision now

```bash
python3 scripts/openclaw-openai-accounts.py auto \
  --inactive-minutes 3 \
  --five-hour-switch-at 80 \
  --five-hour-hard-switch-at 90 \
  --weekly-switch-at 90 \
  --weekly-hard-switch-at 95
```

### 8) Cron-friendly one-line output

```bash
python3 scripts/openclaw-openai-accounts.py cron-check \
  --inactive-minutes 3 \
  --five-hour-switch-at 80 \
  --five-hour-hard-switch-at 90 \
  --weekly-switch-at 90 \
  --weekly-hard-switch-at 95
```

### 9) Recommended OpenClaw cron cadence

Run the above `cron-check` every **10 minutes**.

---

## What gets reconciled automatically

The following commands reconcile live auth state before reporting or switching:

- `list`
- `add`
- `use`
- `auto`
- `cron-check`

That reconciliation includes:

- detecting the **current live** `openai-codex:default` login
- adding newly discovered emails into the saved account list
- updating same-email accounts when team/account/token changes
- updating `openclaw.json` auth aliases
- updating agent `auth-profiles.json` named email aliases
- pruning stale aliases no longer backed by saved accounts

---

## Repository layout

```text
.
├── SKILL.md
├── README.md
└── scripts/
    ├── openclaw-openai-accounts.py
    └── test_openclaw_openai_accounts.py
```

---

## Recent changes

### v1.0.2

- make **email** the canonical account identity
- reconcile live auth back into the saved account list
- auto-add missing `openclaw.json` / auth alias entries
- prune stale aliases that no longer belong to saved accounts
- add session-safe switching guard for active conversations
- move unattended checks to **every 10 minutes**
- add **5-hour soft/hard thresholds**: `80% / 90%`
- add **weekly soft/hard thresholds**: `90% / 95%`
- expand regression coverage to **11 tests**

### v1.0.1

- initial public standalone release packaging

---

## Typical use cases

- “Which OpenAI OAuth account is OpenClaw using right now?”
- “Show all locally saved accounts and their quota state.”
- “Switch to another account safely.”
- “Rotate accounts before the current one hits 5-hour limits.”
- “Avoid switching in the middle of active conversations.”
- “Fall back to Bailian when all OpenAI accounts are exhausted or unusable.”

---

## Safety notes

This skill works with sensitive local auth state and cached quota metadata.

- Do **not** commit local OAuth tokens or runtime credential files
- Review `status` / `list --verbose --probe` before risky operations
- Treat `~/.openclaw/openai-codex-accounts/` as credential material

---

## 中文速览

这个 skill 用于在 **OpenClaw 内部管理多个 OpenAI OAuth / openai-codex 账号**。

核心策略：

- 每 **10 分钟**巡检一次
- 5 小时已用 **80%**：若所有非 cron session 已 **3 分钟不活跃**，尝试切换
- 5 小时已用 **90%**：立即切换
- 每周已用 **90%**：若所有非 cron session 已 **3 分钟不活跃**，尝试切换
- 每周已用 **95%**：立即切换
- 没有合适 OpenAI 账号时，回退到备用模型

---

## Related

- GitHub Releases: <https://github.com/tutouguai1933/openclaw-openai-multi-account/releases>
- ClawHub skill: `openclaw-openai-multi-account`
- Intended for OpenClaw environments using multiple OpenAI OAuth logins
