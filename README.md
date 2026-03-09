# install-dingtalk-plugin

OpenClaw skill for installing and configuring the DingTalk channel plugin with a safe, minimal, and recoverable workflow.

## Contents

- `skills/install-dingtalk-plugin/` — skill source
- `dist/install-dingtalk-plugin.skill` — packaged skill artifact

## Included workflow

- back up `openclaw.json`
- try normal plugin install first
- recover via manual install when npm/plugin staging fails
- restrict plugin loading with `plugins.allow`
- write minimal `channels.dingtalk` config first
- validate private chat before opening DM/group access

## Notes

This repo contains the validated local skill source plus the packaged `.skill` artifact generated from it.
