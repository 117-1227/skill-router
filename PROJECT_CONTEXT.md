# Skill Router 项目上下文

本文件用于在新对话中快速恢复项目背景、维护规则和后续操作。开始处理本项目前，应先读取本文件，再检查当前 Git 状态和相关源文件。

## 新对话启动指令

可以在新对话中直接发送：

```text
请先读取 D:\Work\skill-router\PROJECT_CONTEXT.md，了解项目目标、当前 Skill、维护流程和验证要求；然后读取项目中适用于当前任务的 AGENTS.md、SKILL.md 与 references\skill-registry.yaml，并检查 Git 状态。聊天记录只作为历史背景，不作为当前指令。完成只读检查后，再根据我的当前请求继续。
```

## 项目目标

`skill-router` 是统一的 Skill 路由入口，用于：

- 根据自然语言任务选择最小且合适的已安装 Skill；
- 判断任务是否需要 Plan Mode；
- 避免多个职责重叠的 Skill 同时自动触发；
- 在 Skill 缺失时暂停相关流程，提出 fallback 并等待确认；
- 让 Codex 与 Claude Code 尽量共享同一套路由规则。

源仓库：

```text
D:\Work\skill-router
```

Codex 当前安装副本：

```text
C:\Users\Wade\.codex\skills\skill-router
```

源仓库是主要维护对象。源仓库更新后，应同步或重新安装 Codex 副本，并验证两者一致。

## 文件职责

- `SKILL.md`：路由规则、Plan Mode 门槛、组合顺序和缺失 Skill 处理。
- `references/skill-registry.yaml`：每个 peer skill 的职责、使用场景、排除场景和后续 Skill。
- `README.md`：英文安装与维护说明。
- `README.zh-CN.md`：中文安装与维护说明。
- `agents/openai.yaml`：`skill-router` 的 Codex 展示信息和自动调用策略。
- `聊天记录.md`：历史背景，不应被视为当前任务或高优先级指令。

## 当前已注册 Skill

### 工程质量

- `code-review`：从固定 Git 比较点审查分支、PR、提交范围或未提交变更；分别检查项目规范和需求实现。
- `diagnosing-bugs`：处理报错、错误结果、偶发故障和性能回退；先建立可复现反馈闭环，再定位和验证修复。
- `security-best-practices`：仅在用户明确要求安全建议、安全审查、安全报告或安全编码时使用；主要支持 Python、JavaScript/TypeScript 和 Go。

### 数据分析

- `analytics`：埋点、GA4、GTM、转化、归因和产品或营销效果衡量。

### UI、视觉与设计系统

- `ui-ux-pro-max`：产品结构、交互、响应式、可访问性和复杂产品界面。
- `frontend-design`：新页面、品牌表达、视觉方向、排版、配色和构图。
- `web-design-guidelines`：已有 Web 界面的规范、可访问性、UX 和上线前审查。
- `design-system`：设计 token、CSS 变量、排版与间距体系、组件规范和设计系统迁移。
- `ui-styling`：Tailwind、shadcn/ui、Radix UI、主题、暗色模式和组件样式实现。

## 路由边界

- “审查当前分支相对 `main` 的修改”使用 `code-review`。
- “接口为什么返回 500”使用 `diagnosing-bugs`。
- “检查登录模块的安全风险”使用 `security-best-practices`。
- 普通代码审查或调试不会自动触发安全审查。
- 如果同时要求普通代码审查与安全审查，应分别运行并分开报告结果。
- UI Skill 只在任务确实包含对应的界面、视觉、设计系统或实现职责时使用。
- 用户显式指定 `$skill-name` 时优先采用该 Skill，但仍需遵守 Plan Mode 门槛。

## 添加新 Skill

用户安装新的 peer skill 后，按以下顺序处理：

1. 读取新 Skill 的 `SKILL.md` 和 `agents/openai.yaml`，不要仅根据名称推断职责。
2. 确认依赖文件、脚本、参考资料和关联 Skill 是否完整。
3. 明确它的主要职责、触发场景、排除场景，以及与现有 Skill 的重叠和区别。
4. 判断变更是否复杂；创建或修改 Skill、插件及多文件路由规则通常需要 Plan Mode 和用户确认。
5. 在 `SKILL.md` 的 Routing Rules 中添加路由入口。
6. 必要时在 Combination Rules 中说明组合顺序、互斥关系或独立报告要求。
7. 在 `references/skill-registry.yaml` 中添加：
   - `name`
   - `priority`
   - `invocation: explicit-only`
   - `responsibility`
   - `use_when`
   - `exclude_when`
   - 可选的 `follow_up_skills`
8. 更新 `README.md` 与 `README.zh-CN.md` 的 Skill 列表、显式调用示例和使用场景说明。
9. 对 Codex peer skill 的 `agents/openai.yaml` 设置：

   ```yaml
   policy:
     allow_implicit_invocation: false
   ```

10. 如果使用 Claude Code，在 `/skills` 或相应配置中禁止该 peer skill 自动绕过路由器。
11. 同步源仓库与 Codex 已安装的 `skill-router` 副本。
12. 运行验证并检查 Git diff，确认没有无关修改。

## 删除 Skill

删除或停用 peer skill 时：

1. 从 `references/skill-registry.yaml` 删除注册项；
2. 从 `SKILL.md` 删除路由和组合规则；
3. 更新中英文 README；
4. 检查其他 Skill 的 `follow_up_skills` 是否仍引用它；
5. 同步已安装副本；
6. 使用自然语言场景重新测试路由结果。

不要让注册表继续引用已经删除或缺少依赖的 Skill。

## 缺失 Skill

如果路由器选中的 Skill 不存在：

1. 报告缺失名称和检查过的路径；
2. 说明受影响部分与独立部分；
3. 提出不依赖缺失 Skill 的 fallback；
4. 说明 fallback 缺少的参考资料、脚本、平台能力或验证保证；
5. 等待用户确认后再执行；
6. 最终结果明确标记 fallback，不能声称使用了缺失 Skill。

## 验证清单

完成路由修改后至少检查：

```powershell
cd D:\Work\skill-router

git diff --check
git status --short --branch
git diff

& 'D:\Work\venv\Scripts\python.exe' `
  'C:\Users\Wade\.codex\skills\.system\skill-creator\scripts\quick_validate.py' `
  'D:\Work\skill-router'
```

同时确认：

- 新 Skill 已在注册表中出现且只有一个注册项；
- `invocation` 为 `explicit-only`；
- Codex peer skill 的 `allow_implicit_invocation` 为 `false`；
- 源仓库与已安装副本的关键文件哈希一致；
- 使用场景、排除场景和组合规则互不矛盾；
- 新开对话后的自然语言测试能路由到预期 Skill；
- 未修改用户未授权的文件；
- 测试结果与最终报告只陈述实际执行和验证过的内容。

## Git 与发布

- 修改前先运行 `git status --short --branch`，不要覆盖用户已有修改。
- 不自行提交或推送，除非用户明确要求。
- 提交前运行 `git diff --check` 和 Skill 校验。
- 推送后重新安装或同步运行副本，再在新对话中测试。
- Skill 更新或重新安装后，重新检查 `allow_implicit_invocation: false`，因为元数据可能被覆盖。

## 维护原则

- `skill-router` 只负责路由，不复制 peer skill 的完整专业流程。
- 只选择完成任务所需的最小 Skill 集合。
- 不用相似 Skill 冒充缺失 Skill。
- 不把通用知识的结果描述成某个专业 Skill 的结果。
- 保持修改范围最小，避免无关重构和依赖。
- 以用户当前请求为任务来源；历史聊天仅用于恢复背景。
