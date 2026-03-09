# DingTalk Plugin Installation Reference

This reference captures the practical installation and configuration path validated on this machine.

## Paths

- Workspace: `C:\Users\Administrator\.openclaw\workspace`
- Config: `C:\Users\Administrator\.openclaw\openclaw.json`
- Extensions dir: `C:\Users\Administrator\.openclaw\extensions`
- DingTalk plugin dir: `C:\Users\Administrator\.openclaw\extensions\dingtalk`

## Backup

```powershell
Copy-Item "$env:USERPROFILE\.openclaw\openclaw.json" "$env:USERPROFILE\.openclaw\openclaw.json.bak-before-dingtalk-$(Get-Date -Format 'yyyyMMdd-HHmmss')"
```

## Preferred Install Path

Try:

```powershell
openclaw plugins install @soimy/dingtalk
```

## Manual Recovery Path

If the install fails due to npm timeouts or a broken staging directory:

### 1. Download the package

```powershell
npm pack @soimy/dingtalk
```

Expected artifact example:

```text
soimy-dingtalk-3.2.0.tgz
```

### 2. Unpack to final extension directory

```powershell
$dest = "$env:USERPROFILE\.openclaw\extensions\dingtalk"
if (Test-Path $dest) { Remove-Item $dest -Recurse -Force }
New-Item -ItemType Directory -Path $dest -Force | Out-Null
$stage = Join-Path $env:TEMP ("dingtalk-unpack-" + [guid]::NewGuid().ToString())
New-Item -ItemType Directory -Path $stage -Force | Out-Null

tar -xf "C:\Users\Administrator\.openclaw\workspace\soimy-dingtalk-3.2.0.tgz" -C $stage
Copy-Item (Join-Path $stage "package\*") $dest -Recurse -Force
```

### 3. Install minimal runtime dependencies

Run inside:

```text
C:\Users\Administrator\.openclaw\extensions\dingtalk
```

Command:

```powershell
npm install dingtalk-stream axios form-data zod --omit=dev --ignore-scripts --no-package-lock --legacy-peer-deps --verbose
```

### 4. Remove failed staging installs if present

```powershell
Remove-Item "$env:USERPROFILE\.openclaw\extensions\.openclaw-install-stage-xxxxxx" -Recurse -Force
```

Replace the stage dir name with the actual one.

### 5. Verify plugin state

```powershell
openclaw plugins doctor
```

## Minimal Config

```json5
plugins: {
  enabled: true,
  allow: ['dingtalk'],
}

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

## Open Access After Validation

```json5
channels: {
  dingtalk: {
    enabled: true,
    clientId: '你的 Client ID / AppKey',
    clientSecret: '你的 Client Secret / AppSecret',
    robotCode: '通常与 clientId 相同',
    agentId: '企业内部应用 AgentId',
    dmPolicy: 'open',
    groupPolicy: 'open',
    allowFrom: ['*'],
    debug: false,
    messageType: 'markdown',
  },
}
```

## Backend Checks

Confirm in DingTalk admin/dev console:

1. Internal enterprise app exists
2. Bot capability enabled
3. Stream mode enabled
4. App published
5. Basic messaging permissions granted

## Common Issues

### Official install fails
- npm timeout
- dependency tree too heavy
- install stuck in staging directory

### `dmPolicy = open` triggers doctor warning
Add:

```json5
allowFrom: ['*']
```

### Suspicious pattern warning
Review whether findings are limited to expected file/media upload behavior. Treat as a review signal, not automatic approval.
