# Skill Router

`skill-router` 是一个轻量级技能协调器，用于根据用户的自然语言任务选择合适的已安装 skill，并判断任务是否需要计划模式。

它不会替代或修改专业 peer skill。当前注册的 skill 包括：

- `analytics`
- `frontend-design`
- `ui-ux-pro-max`
- `web-design-guidelines`
- `design-system`
- `ui-styling`

## 自动调用与显式调用

`skill-router` 允许自动调用。任务匹配后，平台可以自动加载它作为统一入口。

peer skill 默认采用显式调用策略。路由器选中 peer skill 后，会直接读取对应的 `SKILL.md`，不依赖平台自动触发 peer skill。

用户可以显式指定 skill，显式选择优先：

```text
$analytics
$frontend-design
$ui-ux-pro-max
$web-design-guidelines
$design-system
$ui-styling
```

## 安装

将此目录发布到 GitHub 后，根据使用的平台安装。

### Codex

使用 Codex 的 GitHub skill installer：

```powershell
python <CODEX_HOME>\skills\.system\skill-installer\scripts\install-skill-from-github.py `
  --url https://github.com/<owner>/<repository>/tree/<ref>/<path-to-skill-router>
```

安装后，skill 位于：

```text
$CODEX_HOME/skills/skill-router
```

### Claude Code

将目录放到以下任一位置：

```text
<project>/.claude/skills/skill-router/SKILL.md
~/.claude/skills/skill-router/SKILL.md
```

Claude Code 使用共享的 `SKILL.md` 和 `references/` 文件。`agents/openai.yaml` 是 Codex 专用元数据，Claude Code 可以忽略。

六个 peer skill 需要分别安装到对应平台。路由器不会复制或替换它们。

## 严格路由策略

`skill-router` 是唯一的自动入口。

Codex 中，每个 peer skill 应包含：

```yaml
policy:
  allow_implicit_invocation: false
```

Claude Code 中，应通过 `skillOverrides` 设置或 `/skills` 配置界面隐藏 peer skill 的模型自动调用。不要把 Claude 专用 frontmatter 添加到共享的 `SKILL.md`，以免影响 Codex 兼容性。

`references/skill-registry.yaml` 中，每个注册项应包含：

```yaml
invocation: explicit-only
```

## 计划模式

复杂任务应先经过 `skill-router` 判断。

复杂任务包括：

- 多文件或多模块修改；
- 架构、重构或迁移；
- 部署、生产环境、数据库或权限；
- 外部服务；
- 多个 skill 协作；
- 需求存在关键歧义；
- 创建或修改 skill、插件或自动化流程。

如果任务复杂且当前平台尚未进入计划模式，路由器应停止并提醒用户手动开启平台的计划模式。

计划模式下应先检查环境、解决关键决策、输出完整计划，并等待用户确认。计划确认前不得修改文件或执行外部写入。

## 缺失 Peer Skill

如果注册的 peer skill 不存在，路由器应暂停受影响的工作流：

1. 报告缺失的 skill 名称和检查过的路径；
2. 说明该 skill 对任务的影响；
3. 区分受影响部分和独立部分；
4. 提出不依赖缺失 skill 的替代方案；
5. 说明替代方案缺少的脚本、参考资料、平台能力或验证保证；
6. 等待用户确认后再继续。

不得静默使用相似 skill 替代，也不得声称使用了缺失的 skill。

示例：

```text
原方案：
skill-router -> design-system -> ui-styling

缺失：
design-system 不可用。

更新后的替代方案：
skill-router -> 用户确认
-> 使用通用三层 token 方法
-> ui-styling
-> 缩小验证范围，并在结果中标记 fallback
```

## 添加新 Skill

新增 skill 时：

1. 安装新 skill；
2. 在 `references/skill-registry.yaml` 中注册；
3. 添加 `name`、`responsibility`、`use_when`、`exclude_when` 和 `invocation: explicit-only`；
4. Codex 添加 `allow_implicit_invocation: false`；
5. Claude Code 在 `/skills` 中隐藏该 peer skill 的自动调用；
6. 测试路由器是否能读取新 skill 的 `SKILL.md`。

完整的专业工作流应保留在新 skill 自己的 `SKILL.md` 中，不要复制到路由器。
