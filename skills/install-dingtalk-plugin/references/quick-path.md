# 钉钉插件快速路径

当用户只想要最短、最稳的接入路径时，可直接按下面步骤执行。

---

## 1. 先备份配置

```powershell
Copy-Item "$env:USERPROFILE\.openclaw\openclaw.json" "$env:USERPROFILE\.openclaw\openclaw.json.bak-before-dingtalk-$(Get-Date -Format 'yyyyMMdd-HHmmss')"
```

---

## 2. 先尝试正常安装

```powershell
openclaw plugins install @soimy/dingtalk
```

如果安装失败，再改走手动恢复安装路径。

---

## 3. 限制插件加载范围

```json5
plugins: {
  enabled: true,
  allow: ['dingtalk'],
}
```

---

## 4. 写入最小可用配置

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

---

## 5. 先验证私聊

确认私聊链路正常后，再放开更开放的策略。

---

## 6. 放开私聊和群聊时，补上 `allowFrom`

```json5
allowFrom: ['*']
```

---

## 一句话总结

**最短路径就是：备份 → 安装 → 白名单 → 最小配置 → 私聊验证 → 再开放。**
