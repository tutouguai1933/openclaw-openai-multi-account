# Contributing

Thanks for your interest in improving **OpenClaw OpenAI Multi Account**.

## Scope

This repository contains the standalone skill package for managing **multiple OpenAI OAuth login accounts inside OpenClaw**.

Please keep changes aligned with that scope:

- OpenClaw multi-account workflow
- OpenAI OAuth account switching
- quota/status inspection
- account snapshot management
- fallback / auto-rotation behavior
- documentation for operators

## Development guidelines

1. Keep the skill description accurate and operator-oriented.
2. Do not commit local secrets, tokens, auth caches, or personal runtime files.
3. Prefer clear, auditable behavior over hidden automation.
4. Preserve compatibility with the OpenClaw workflow described in `SKILL.md`.
5. When changing behavior, update both documentation and release notes.

## Files of interest

- `SKILL.md` — the published skill definition and usage guide
- `scripts/openclaw-openai-accounts.py` — main implementation
- `scripts/test_openclaw_openai_accounts.py` — regression tests
- `README.md` — GitHub-facing overview

## Suggested workflow

```bash
python3 scripts/test_openclaw_openai_accounts.py
```

Then review the skill documentation for consistency.

## Pull requests

Please include:

- what changed
- why it changed
- any user-visible behavior changes
- whether quota/account switching logic changed
- whether documentation was updated

## Security

If an issue may expose credentials, OAuth tokens, or local auth state, please avoid posting sensitive data publicly.
