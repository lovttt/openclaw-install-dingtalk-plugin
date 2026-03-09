# install-dingtalk-plugin

这是一个用于 **OpenClaw 接入钉钉（DingTalk）插件** 的技能仓库，包含：

- `skills/install-dingtalk-plugin/` —— 技能源码
- `dist/install-dingtalk-plugin.skill` —— 已打包好的技能文件

## 这个仓库能做什么

这个技能用于帮助 OpenClaw 以**尽量安全、最小改动、可回退**的方式接入钉钉。

适用场景包括：

- 安装 `@soimy/dingtalk` 插件
- 修复钉钉插件安装失败
- 配置 `plugins.allow`
- 写入 `channels.dingtalk`
- 先用保守策略验证，再放开私聊和群聊

## 包含的流程

技能里已经整理了下面这些实际可复用步骤：

1. 备份 `openclaw.json`
2. 先尝试正常安装插件
3. 如果 npm / staging 安装失败，走手动恢复安装路径
4. 使用 `plugins.allow` 限制插件加载
5. 先写最小可用的 `channels.dingtalk` 配置
6. 先验证私聊，再放开群聊和开放策略

## 仓库结构

```text
skills/install-dingtalk-plugin/   技能源码
  ├─ SKILL.md
  └─ references/
      ├─ manual-install.md
      └─ quick-path.md

dist/install-dingtalk-plugin.skill 打包产物
```

## 说明

这个仓库同时保留：

- 可维护的技能源码
- 可直接分发/导入的 `.skill` 打包文件

如果你只是想直接使用，优先看：

- `dist/install-dingtalk-plugin.skill`

如果你想继续维护或修改流程，优先看：

- `skills/install-dingtalk-plugin/`
