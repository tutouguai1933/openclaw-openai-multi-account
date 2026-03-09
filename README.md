# OpenClaw OpenAI Multi Account

A skill for managing multiple OpenAI OAuth login accounts inside OpenClaw, especially multiple `openai-codex` OAuth accounts.

## What it supports

- Snapshot and switch OpenClaw OpenAI OAuth accounts
- Detect the real active account
- Inspect cached 5-hour / weekly quota
- Auto-repair ACTIVE metadata drift
- Auto-enroll newly logged-in accounts
- Auto-rotate same-model accounts near exhaustion
- Fall back to a backup model when all OpenAI accounts are unavailable

## Main files

- `SKILL.md`
- `scripts/openclaw-openai-accounts.py`
- `scripts/test_openclaw_openai_accounts.py`

## Notes

This repository publishes the standalone skill content extracted from a local OpenClaw workspace.
Sensitive local runtime state and tokens are **not** included in this repository.
