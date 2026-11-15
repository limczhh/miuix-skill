# Miuix Skills

本仓库是 Miuix Compose UI 的开发参考，提供两个相互配合、但可以独立发现和安装的 Skill：

- **miuix-components**：回答组件库“有什么、真实 API 怎么调用、需要什么模块和宿主”。
- **miuix-guidelines**：回答页面“如何组织骨架、所有权、层级、Insets 和组件组合”。

仓库根目录只是源码仓库和维护说明，不是第三个 Skill。当前维护的 Miuix 证据版本是 **v0.9.4**，固定在提交 **39c40f99844227b853f0049a0933b1f3ae6c00ba**。

## 两个 Skill

| Skill | 适用问题 | 入口 |
|---|---|---|
| [miuix-components](./miuix-components/SKILL.md) | 组件能力、确切签名、导入和模块、默认值、宿主、生命周期、版本和源码核验 | **miuix-components/SKILL.md** |
| [miuix-guidelines](./miuix-guidelines/SKILL.md) | 页面骨架、状态和所有权、Scaffold 组合、层级、间距、Insets、自适应和界面审查 | **miuix-guidelines/SKILL.md** |

### 如何选择

- 只问“这个组件真实叫什么、参数是什么、从哪个模块导入、为什么编译报错”时，使用 **miuix-components**。
- 新建、重构或审查页面，涉及页面骨架、滚动、导航、弹窗、状态归属或视觉层级时，使用 **miuix-guidelines**。
- 同时涉及设计和实现时，先由 **miuix-guidelines** 确定页面结构，再由 **miuix-components** 核对实际调用到的 Miuix 符号。
- 不需要为了一个组件问题读取另一个 Skill 的全部 Reference。

## Skill 的实际流程

### 组件库流程

1. 先确认目标项目的 Miuix 版本、平台、source set、依赖、已有主题和宿主。
2. 按问题选择一个入口：
   - 想了解 Miuix 整体有什么、模块如何分工或该从哪个依赖开始：读 **library-overview.md**。
   - 不认识源码区域：读 **library-map.md**。
   - 不确定语义组件家族：读 **component-selection.md**。
   - 已知组件但需要准确调用：读 **api-contracts.md**。
   - API 未覆盖、版本不同或行为不确定：读 **source-verification.md**。
3. 回到固定版本源码，确认完整包名、模块、重载、参数、默认值、opt-in、宿主和生命周期。
4. 只在需要时用文档、Demo 或 Example 补充用途和组合证据；它们不能代替源码声明。
5. 写最小调用，并在目标项目可用时编译。
6. 分开报告 source-verified、compile-verified、预览和设备验证；没有编译就不能声称已编译通过。

### 页面规范流程

1. 先确认已有主题、路由 owner、页面边界、页面 host、平台和 source set。
2. 先填写页面骨架：路由/导航 owner、shell/page host、主内容或滚动 owner、Insets 交接与最终 consumer、host slots、瞬态层和持久状态 owner。
3. 再按语义选择组件；页面是设置页、列表页还是表单页，不会自动决定组件。
4. 只把最终确实要调用的 Miuix 符号交给 **miuix-components** 核对。
5. 根据已核对的 API 写组合代码，保留目标项目已有的行为和状态归属。
6. 编译后再分别检查预览、渲染、设备、自适应、Insets、无障碍和状态变化。

两者组合时，边界是：

```text
miuix-guidelines：页面骨架和组合关系
        ↓
miuix-components：具体符号的 API、模块和源码合同
        ↓
miuix-guidelines：回到页面组合并完成审查
        ↓
目标项目：编译、预览或设备验证
```

## 目录结构

```text
miuix-skill/
├── .agents/plugins/marketplace.json
├── .codex-plugin/plugin.json
├── miuix-components/
│   ├── SKILL.md
│   └── references/
└── miuix-guidelines/
    ├── SKILL.md
    └── references/
```

每个 `miuix-*` 子目录都是一个独立 Skill 根目录，使用环境需要在该目录的根部直接看到 **SKILL.md**。`.codex-plugin/` 和 `.agents/plugins/` 只负责把两个 Skill 作为一个可安装包分发，不是新的 Skill。

## 安装

### Codex：从 Git Marketplace 安装（可选）

仓库提供了 Codex 兼容插件清单，并将两个同级 Skill 显式列入插件包。这里的 Marketplace 是由仓库维护的 Git 插件源，不是 OpenAI 公共插件目录；`marketplace add` 只是登记并跟踪 Git 源，不代表本仓库已经上传或发布到公共市场。必须先把这些清单提交并推送到 GitHub，远程安装才看得到它们。

```bash
codex plugin marketplace add limczhh/miuix-skill
codex plugin add miuix-skills@miuix-skill
```

更新插件来源：

```bash
codex plugin marketplace upgrade miuix-skill
```

安装或更新后，按当前客户端提示重新载入插件或开始新会话。插件安装的是一个包，但 `miuix-components` 和 `miuix-guidelines` 仍然是两个独立、可分别触发的 Skill。清单位于 [.codex-plugin/plugin.json](.codex-plugin/plugin.json) 和 [.agents/plugins/marketplace.json](.agents/plugins/marketplace.json)；插件打包规则见 [OpenAI Plugins 文档](https://developers.openai.com/plugins/build/plugins)。

本仓库目前没有提交到 OpenAI 公共插件目录。若要进入公共目录，还需要准备符合公共插件格式的发布包，提交审核并由发布流程上线；那是另一条分发路径，不是下面这条 Git Marketplace 命令的结果。

如果当前环境没有可用的插件命令，使用下面的直接安装方式；不要把仓库根目录当作一个 Skill。

### 直接安装（推荐用于只安装 Skill）

下面这种路径不应作为可靠安装方式：

```text
~/.codex/skills/miuix/miuix-components/SKILL.md
~/.codex/skills/miuix/miuix-guidelines/SKILL.md
```

因为 **SKILL.md** 在 **miuix** 的二级目录中，而 **miuix** 根部没有 **SKILL.md**。除非宿主明确实现了递归发现，否则系统不会把这两个目录当作已安装 Skill；本仓库不依赖这种行为。

可靠的目标路径应当是：

```text
~/.codex/skills/miuix-components/SKILL.md
~/.codex/skills/miuix-guidelines/SKILL.md
```

先把 Git 仓库存放在 Skill 搜索目录之外，作为更新源。PowerShell：

```powershell
$sourceRoot = "$env:USERPROFILE\.codex\skill-sources"
$repoRoot = "$sourceRoot\miuix-skill"
$skillRoot = "$env:USERPROFILE\.codex\skills"

New-Item -ItemType Directory -Force -Path $sourceRoot | Out-Null
git clone https://github.com/limczhh/miuix-skill.git $repoRoot
```

然后把两个 Skill 的内容分别复制到搜索目录：

```powershell
New-Item -ItemType Directory -Force -Path "$skillRoot\miuix-components" | Out-Null
New-Item -ItemType Directory -Force -Path "$skillRoot\miuix-guidelines" | Out-Null

Copy-Item -Path "$repoRoot\miuix-components\*" -Destination "$skillRoot\miuix-components" -Recurse -Force
Copy-Item -Path "$repoRoot\miuix-guidelines\*" -Destination "$skillRoot\miuix-guidelines" -Recurse -Force
```

macOS/Linux 可以直接执行：

```bash
git clone https://github.com/limczhh/miuix-skill.git ~/.codex/skill-sources/miuix-skill
mkdir -p ~/.codex/skills/miuix-components ~/.codex/skills/miuix-guidelines
cp -R ~/.codex/skill-sources/miuix-skill/miuix-components/. ~/.codex/skills/miuix-components/
cp -R ~/.codex/skill-sources/miuix-skill/miuix-guidelines/. ~/.codex/skills/miuix-guidelines/
```

如果使用环境允许目录链接，也可以把两个子目录分别链接到 ~/.codex/skills/；链接的目标仍必须是 miuix-components 和 miuix-guidelines，不能链接仓库根目录。

更新时在源码仓库执行：

```bash
git -C ~/.codex/skill-sources/miuix-skill pull --ff-only
```

复制安装需要重新执行上面的复制命令；目录链接则只需更新源码仓库。

### 项目内安装

项目内安装使用相同的目录层级：

```text
<project>/.codex/skills/miuix-components/SKILL.md
<project>/.codex/skills/miuix-guidelines/SKILL.md
```

不要使用：

```text
<project>/.codex/skills/miuix/miuix-components/SKILL.md
```

Claude Code 等使用 .claude/skills/ 的宿主也遵循同一原则：把两个 Skill 子目录分别放到 .claude/skills/miuix-components/ 和 .claude/skills/miuix-guidelines/，不要把仓库根目录当作单个 Skill。

## 使用和触发

安装完成后，不需要在每次请求中手动指定 Reference。直接描述任务即可让宿主按 Skill 的描述选择入口：

- 需要确认组件名称、参数、导入、模块或编译错误时，明确提出 API 或依赖问题，使用 **miuix-components**。
- 需要新建、重构或审查页面时，说明页面目标和现有宿主，使用 **miuix-guidelines**。
- 同时涉及页面结构和具体调用时，先确定页面骨架，再核对最终实际调用的 Miuix API。

例如：

```text
确认 Miuix v0.9.4 中 FloatingNavigationBar 的真实包名、参数、模块和 Insets 行为，并给出目标项目可编译的最小调用。
```

```text
重构这个 Miuix 页面：先确定 Scaffold、滚动内容、折叠顶栏和底部导航的 owner，再实现并检查 Insets 和状态归属。
```

## Reference 的读取方式

两个入口的 SKILL.md 都只负责路由和工作约束。实际使用时：

1. 先读取当前触发的 Skill 入口。
2. 选择一个最相关的 Primary Reference。
3. 只读取该文档中解决当前问题的标题、表格或合同卡片。
4. 只有当前问题仍缺少证据时，才继续打开下一个 Reference。
5. 组件 API、页面组合、Example 证据和目标项目编译结果始终分开记录。

不应把整个 references/ 目录预加载成百科全书。

## 证据范围

- Miuix 版本：v0.9.4
- 固定提交：39c40f99844227b853f0049a0933b1f3ae6c00ba
- 上游仓库：[compose-miuix-ui/miuix](https://github.com/compose-miuix-ui/miuix)
- 固定版本文档源：[Miuix v0.9.4 docs](https://github.com/compose-miuix-ui/miuix/tree/39c40f99844227b853f0049a0933b1f3ae6c00ba/docs)

如果目标项目使用其他版本，先标记版本差异，再核对目标项目实际源码；不能直接把固定版本合同当成目标项目已验证 API。
