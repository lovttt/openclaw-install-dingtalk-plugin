---
name: install-dingtalk-plugin
description: Install and configure the OpenClaw DingTalk channel plugin with a safe, minimal, and recoverable workflow. Use when setting up DingTalk Stream mode for OpenClaw, auditing or installing @soimy/dingtalk, recovering from failed plugin installs, adding plugins.allow for dingtalk, writing channels.dingtalk config, or opening DM/group access after initial validation.
---

# Install DingTalk Plugin

Use this skill to set up DingTalk for OpenClaw in the safest practical order.

## Workflow

1. Back up `~/.openclaw/openclaw.json` before changing config.
2. Prefer `openclaw plugins install @soimy/dingtalk` first.
3. If plugin install fails because of npm timeouts or a broken staging install, use the manual recovery workflow in `references/manual-install.md`.
4. Restrict plugin loading with `plugins.allow`, starting with `['dingtalk']`.
5. Write a minimal `channels.dingtalk` config first:
   - `enabled: true`
   - `clientId`
   - `clientSecret`
   - `robotCode`
   - `agentId`
   - `dmPolicy: 'pairing'`
   - `groupPolicy: 'allowlist'`
   - `messageType: 'markdown'`
6. Validate private chat first.
7. Only after validation, open access:
   - `dmPolicy: 'open'`
   - `groupPolicy: 'open'`
   - `allowFrom: ['*']`
8. Keep card/media features off until basic messaging is stable.

## Safety Rules

- Do not overwrite the whole config unless necessary; prefer minimal changes.
- Always keep or create a backup before changing config.
- Treat suspicious-code findings on DingTalk plugins as a review signal, not automatic approval.
- Distinguish clearly between plugin install failures and config failures.
- If npm/network is flaky, avoid repeated heavy metadata fetches; use the known-good manual install path.

## What to Read

- For the complete operational runbook, read `references/manual-install.md`.

## Expected Outcomes

A successful setup should end with:
- dingtalk plugin present under `~/.openclaw/extensions/dingtalk`
- `plugins.allow` including `dingtalk`
- `channels.dingtalk` configured
- private chat working first, then group mentions if requested
