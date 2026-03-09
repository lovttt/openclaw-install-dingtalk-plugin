# Quick Path

Use this when the user wants the shortest safe path.

## 1. Back up config

```powershell
Copy-Item "$env:USERPROFILE\.openclaw\openclaw.json" "$env:USERPROFILE\.openclaw\openclaw.json.bak-before-dingtalk-$(Get-Date -Format 'yyyyMMdd-HHmmss')"
```

## 2. Try normal install

```powershell
openclaw plugins install @soimy/dingtalk
```

## 3. Lock plugin loading

```json5
plugins: {
  enabled: true,
  allow: ['dingtalk'],
}
```

## 4. Write minimal config

```json5
channels: {
  dingtalk: {
    enabled: true,
    clientId: '你的 Client ID / AppKey',
    clientSecret: '你的 Client Secret / AppSecret',
    robotCode: '通常与 clientId 相同',
    agentId: '企业内部应用 AgentId',
    dmPolicy: 'pairing',
    groupPolicy: 'allowlist',
    debug: false,
    messageType: 'markdown',
  },
}
```

## 5. Validate private chat

Only after validation, open DM/group access and add:

```json5
allowFrom: ['*']
```
