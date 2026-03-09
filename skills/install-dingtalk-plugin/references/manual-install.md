# 钉钉插件安装参考手册

本文档用于说明如何在 OpenClaw 中安装并配置钉钉（DingTalk）插件，重点强调：

- 先备份
- 先最小配置
- 先验证私聊
- 最后再放开群聊与开放策略

适合在以下场景使用：

- 正常安装钉钉插件
- 插件安装失败后的手动恢复
- 写入 `plugins.allow`
- 配置 `channels.dingtalk`
- 验证并放开私聊 / 群聊

---

## 一、相关路径

- 工作区：`C:\Users\Administrator\.openclaw\workspace`
- 配置文件：`C:\Users\Administrator\.openclaw\openclaw.json`
- 插件目录：`C:\Users\Administrator\.openclaw\extensions`
- 钉钉插件目录：`C:\Users\Administrator\.openclaw\extensions\dingtalk`

---

## 二、安装前先备份配置

在修改配置前，建议先备份：

```powershell
Copy-Item "$env:USERPROFILE\.openclaw\openclaw.json" "$env:USERPROFILE\.openclaw\openclaw.json.bak-before-dingtalk-$(Get-Date -Format 'yyyyMMdd-HHmmss')"
```

这样即使后续配置失败，也能快速回退。

---

## 三、优先使用标准安装方式

先尝试：

```powershell
openclaw plugins install @soimy/dingtalk
```

如果安装成功，可直接进入配置阶段。

---

## 四、标准安装失败时的手动恢复流程

如果遇到如下问题：

- npm 超时
- 插件安装卡在 staging 目录
- 依赖安装未完成

可以改走手动恢复路径。

### 1. 下载插件包

```powershell
npm pack @soimy/dingtalk
```

通常会生成类似文件：

```text
soimy-dingtalk-3.2.0.tgz
```

### 2. 手动解包到最终插件目录

```powershell
$dest = "$env:USERPROFILE\.openclaw\extensions\dingtalk"
if (Test-Path $dest) { Remove-Item $dest -Recurse -Force }
New-Item -ItemType Directory -Path $dest -Force | Out-Null
$stage = Join-Path $env:TEMP ("dingtalk-unpack-" + [guid]::NewGuid().ToString())
New-Item -ItemType Directory -Path $stage -Force | Out-Null

tar -xf "C:\Users\Administrator\.openclaw\workspace\soimy-dingtalk-3.2.0.tgz" -C $stage
Copy-Item (Join-Path $stage "package\*") $dest -Recurse -Force
```

### 3. 安装最小运行依赖

在目录：

```text
C:\Users\Administrator\.openclaw\extensions\dingtalk
```

执行：

```powershell
npm install dingtalk-stream axios form-data zod --omit=dev --ignore-scripts --no-package-lock --legacy-peer-deps --verbose
```

### 4. 清理失败安装残留目录

如果存在失败安装留下的 staging 目录，需清理：

```powershell
Remove-Item "$env:USERPROFILE\.openclaw\extensions\.openclaw-install-stage-xxxxxx" -Recurse -Force
```

请根据实际目录名替换 `xxxxxx`。

### 5. 验证插件状态

```powershell
openclaw plugins doctor
```

目标是确认：

- 插件已被识别
- 没有继续残留 staging 冲突
- 若有安全提示，也要人工判断是否属于功能型风险

---

## 五、推荐的最小配置

建议先使用最小可用配置：

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

### 字段说明

- `clientId`：钉钉应用 AppKey
- `clientSecret`：钉钉应用 AppSecret
- `robotCode`：一般可与 `clientId` 保持一致
- `agentId`：企业内部应用 AgentId
- `dmPolicy: 'pairing'`：先仅允许配对私聊
- `groupPolicy: 'allowlist'`：先不要一上来对所有群开放
- `messageType: 'markdown'`：先使用最稳定的回复形式

---

## 六、验证流程

建议先做最小验证：

1. 在钉钉里私聊机器人
2. 确认 OpenClaw 能收到并正常回复

只在私聊验证成功后，再继续放开更开放的策略。

---

## 七、验证后放开私聊与群聊

验证通过后，可改成开放配置：

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

### 说明

- `dmPolicy: 'open'`：私聊无需 pairing
- `groupPolicy: 'open'`：群里可直接 @ 机器人
- `allowFrom: ['*']`：与 open 策略配套，避免 doctor 警告

---

## 八、钉钉后台需要确认的事项

在钉钉开发者后台确认：

1. 已创建企业内部应用
2. 已启用机器人能力
3. 已开启 Stream 模式
4. 应用已发布
5. 已授予基础消息收发权限

如果后续需要发送图片、文件等媒体，再补充相关权限。

---

## 九、常见问题

### 1. 官方安装失败

常见原因包括：

- npm 网络超时
- 依赖树过重
- 插件安装卡在临时目录

解决思路：

- `npm pack`
- 手动解包到插件目录
- 仅安装最小运行依赖

### 2. `dmPolicy = open` 后出现 doctor 告警

可补充：

```json5
allowFrom: ['*']
```

### 3. 出现 suspicious pattern 提示

需要重点判断是否仅涉及：

- 媒体文件读取
- 文件上传到钉钉
- 下载远程媒体后再转发

如果是上述能力，通常属于功能型风险，需要人工审阅，但不一定是恶意代码。

---

## 十、推荐执行顺序

建议按以下顺序实施：

1. 备份配置
2. 安装插件
3. 加入 `plugins.allow`
4. 写入最小配置
5. 验证私聊
6. 放开私聊与群聊
7. 最后再考虑卡片、媒体、debug 等高级功能

---

## 一句话总结

**先备份、先最小配置、先私聊验证、最后再开放群聊，是最稳的钉钉接入路径。**
