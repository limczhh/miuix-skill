# Miuix Skills

本仓库是 Miuix Compose UI 的开发参考，提供两个相互配合、但可以独立发现和安装的 Skill：

- **miuix-components（路书）**：回答组件库“有什么、真实 API 怎么调用、需要什么模块和宿主”。做到 **100% 契约准确**，同时**精简克制**——只保留高频 API、关键参数/默认值与编译避坑点，不做冗长的源码全量搬运。
- **miuix-guidelines（准则）**：回答页面“如何组织骨架、所有权、层级、Insets 和组件组合”。作为设计规范，严格区分 **强制性红线规范（rules）** 与 **非强制性启发指南（guides/workflows）**。

仓库根目录只是源码仓库和维护说明，不是第三个 Skill。两个 Skill **仅服务 Miuix `0.9.5-beta01`**，固定标签 `v0.9.5-beta01`、提交 **6d2554a850b8960e2616c968263ff64beb5ffb03**。依赖示例使用完整 beta 版本号；不维护旧版本 API、兼容分支或历史迁移文档。目标依赖不符时说明范围差异，依赖升级仍按用户授权执行。

## 当前版本与 Glass

- [依赖、主题与平台设置](./miuix-components/references/setup-and-theme.md)：beta01 模块选择、Android API 24/33 边界、主题与宿主。
- [Glass 替换与共存](./miuix-guidelines/references/glass-migration.md)：旧组件对应关系、仅替换 Overlay 表面、完整组件迁移、普通与 Glass 两套外观共享状态。
- [Glass 组件与 API 边界](./miuix-components/references/glass.md)：新增侧栏、悬浮按钮/工具栏，以及采样、降级、宿主和参数差异。

普通组件与 Glass 家族都使用 beta01。Glass 可选，运行时效果降级仍需保留；组件替换时遵循强制规范中的采样、交互、状态和宿主要求。文档只保留当前用法，过时内容直接修订或删除，避免追加成多版本百科。

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

1. 确认目标实际解析到 `0.9.5-beta01`，以及相关平台、宿主和本地替换；构建输入不变时复用这个结论。
2. 普通需求按语义选择组件；已知符号直接打开 [API 索引](./miuix-components/references/api-contracts.md) 中对应章节，不必先读库概览或组件目录。
3. 标准 beta01 的已覆盖调用复用合同，标记 `catalog-verified`；未覆盖、源码被替换或行为仍不明确时，才按 [源码核验](./miuix-components/references/source-verification.md) 调查具体缺口。
4. 写最小调用，在目标可用时编译；源码、编译、运行与设备效果分别报告。其他 Miuix 版本属于范围不符，不新增兼容分支，也不因此自动升级依赖。

### 页面规范流程

`miuix-guidelines` 保留为一个 Skill，内部区分入口、指南和强制规范：`SKILL.md` 负责识别任务与路由；指南解释页面设计、组合和迁移决策；`references/rules/` 定义强制规范。实现或审查页面时，先读 [design-system.md 的适用范围、例外和主题路由](./miuix-guidelines/references/rules/design-system.md#required-topic-rules)，再按本次任务读取相应条款；新页面执行全部适用规则，已有页面在本次修改范围内执行。

| 强制规范 | 覆盖范围 |
|---|---|
| [design-system.md](./miuix-guidelines/references/rules/design-system.md) | 规则强度和例外、组件与 Defaults、主题和系统外观、语义色、文字、形状、分组、图标和标签、本地化、无障碍 |
| [page-shell.md](./miuix-guidelines/references/rules/page-shell.md) | 页面和公共包装、路由与返回、导航栏/侧栏、Tab/Pager、Insets/IME、滚动/刷新/触感、模糊与 Glass |
| [settings-and-overlays.md](./miuix-guidelines/references/rules/settings-and-overlays.md) | 依赖设置、行与开关独立操作、偏好契约、输入校验、搜索、弹层生命周期、对话框、底部面板、选择与排序 |
| [state-and-performance.md](./miuix-guidelines/references/rules/state-and-performance.md) | 状态和生命周期、持久化和外部消费者、异步与加载、列表和图标缓存、反馈和错误、动画、权限和外链、导入导出 |

普通可滚动页面按 [TopBar 强制规范](./miuix-guidelines/references/rules/page-shell.md#standard-top-app-bar) 采用共享模糊背景层和自适应顶栏：窄窗大标题随滚动收起，宽窗固定紧凑标题，模糊关闭或不可用时不透明降级。顶栏与底栏/侧栏共用实际窗口尺寸策略；特殊头图页面保留有依据的结构。普通模糊与可选的 Glass 组件家族分别路由。

标准刷新页由列表统一持有顶栏滚动连接。公共边距按各边所需安全距离合并，并明确侧栏已占用的起始侧，不要求复制完整的 Example Insets 架构。普通滚动联动与 beta01 回弹视觉通道分别核验，不添加反射或重复控制器。弹层保留到退出结束，关闭中的按下态与新打开会话的草稿分别处理；搜索恢复查询和稳定展开状态。

规范以本套设计系统自身的条款表述。项目代码用于核对实际写法，示例用于解释组合；二者都不意味着把专用 helper、业务名词、偶发写法或某个版本的 API 当成通用要求。

`references/rules/` 是 UI 强制条款的唯一维护位置。其余 reference 保留流程、决策方法、诊断和示例，通过链接应用规范；API 默认值与版本事实由 `miuix-components` 维护。重复的路由索引和综合规则摘录不再另设文件。

1. 先确认已有主题、路由 owner、页面边界、页面 host、平台和 source set。
2. 新页面或结构改动先明确页面骨架与所有权；局部修改只检查受影响的 owner、调用方及条款，不强制填写整页工作表。
3. 再按语义选择组件；页面是设置页、列表页还是表单页，不会自动决定组件。
4. 只把最终确实要调用的 Miuix 符号交给 **miuix-components** 核对。
5. 根据已核对的 API 写组合代码，保留目标项目已有的行为和状态归属。
6. 检查适用规范是否满足，说明有理由的偏离与尚未解决项；编译后再分别检查预览、渲染、设备、自适应、Insets、无障碍和状态变化。规范符合情况、API 核验、编译和视觉验证分别报告。

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
    ├── SKILL.md                       # 任务识别和流程路由
    └── references/
        ├── design-language.md         # 设计决策指南（其余指南同级）
        └── rules/
            ├── design-system.md       # 共享规范、视觉规则和主题路由
            ├── page-shell.md          # 页面、导航、Insets 和手势
            ├── settings-and-overlays.md # 设置、搜索、输入和弹层
            └── state-and-performance.md # 状态、异步、性能和平台操作
```

每个 `miuix-*` 子目录都是一个独立 Skill 根目录，使用环境需要在该目录的根部直接看到 **SKILL.md**。`.codex-plugin/` 和 `.agents/plugins/` 只负责把两个 Skill 作为一个可安装包分发，不是新的 Skill。

## 安装

仓库包含两个同级 Skill。安装后，宿主的 Skill 搜索目录应当直接包含：

```text
<skill-root>/miuix-components/SKILL.md
<skill-root>/miuix-guidelines/SKILL.md
```

不要把整个仓库放成 `<skill-root>/miuix/`；这样会多出一层目录，除非宿主明确支持递归发现。

### Codex 插件安装

支持 `codex plugin` 时：

```bash
codex plugin marketplace add limczhh/miuix-skill
codex plugin add miuix-skills@miuix-skill
```

更新：

```bash
codex plugin marketplace upgrade miuix-skill
```

不支持插件命令时，手动复制两个 Skill 子目录。PowerShell：

```powershell
$sourceRoot = "$env:USERPROFILE\.codex\skill-sources"
$repoRoot = "$sourceRoot\miuix-skill"
$skillRoot = "$env:USERPROFILE\.codex\skills"

New-Item -ItemType Directory -Force -Path $sourceRoot | Out-Null
git clone https://github.com/limczhh/miuix-skill.git $repoRoot
New-Item -ItemType Directory -Force -Path "$skillRoot\miuix-components" | Out-Null
New-Item -ItemType Directory -Force -Path "$skillRoot\miuix-guidelines" | Out-Null
Copy-Item -Path "$repoRoot\miuix-components\*" -Destination "$skillRoot\miuix-components" -Recurse -Force
Copy-Item -Path "$repoRoot\miuix-guidelines\*" -Destination "$skillRoot\miuix-guidelines" -Recurse -Force
```

macOS/Linux：

```bash
mkdir -p ~/.codex/skill-sources ~/.codex/skills
git clone https://github.com/limczhh/miuix-skill.git ~/.codex/skill-sources/miuix-skill
cp -R ~/.codex/skill-sources/miuix-skill/miuix-components ~/.codex/skill-sources/miuix-skill/miuix-guidelines ~/.codex/skills/
```

更新手动安装：在源码仓库执行 `git pull --ff-only`，再重新复制两个子目录。项目内安装也遵循同一目录规则：将它们放到 `<project>/.codex/skills/` 或 `<project>/.claude/skills/` 下。

## 使用和触发

安装完成后，不需要在每次请求中手动指定 Reference。直接描述任务即可让宿主按 Skill 的描述选择入口：

- 需要确认组件名称、参数、导入、模块或编译错误时，明确提出 API 或依赖问题，使用 **miuix-components**。
- 需要新建、重构或审查页面时，说明页面目标和现有宿主，使用 **miuix-guidelines**。
- 同时涉及页面结构和具体调用时，先确定页面骨架，再核对最终实际调用的 Miuix API。

普通用户不需要先知道 Miuix 的组件名，可以直接描述想要的结果，例如：

```text
我想做一个普通的设置页面，顶部有标题，下面有很多选项，先帮我把页面骨架理顺。
```

```text
页面太挤了，底下还想放几个页面入口，帮我看看应该怎么组织。
```

```text
我想加一个搜索框，输入以后在下面显示结果，但不知道该用什么。
```

```text
我想让顶部的大标题跟着内容滚动时收起来，还要有一个可以选择值的设置项。
```

Skill 会先把这些描述转换成页面骨架、组件语义和需要核对的源码合同；只有在真正需要写调用时，才继续查精确 API。已知组件名时也可以直接提问：

```text
确认当前 Miuix 源码中 FloatingNavigationBar 的真实包名、参数、模块和 Insets 行为，并结合我的项目版本给出可编译的最小调用。
```

## Reference 的读取方式

两个入口的 SKILL.md 都负责路由和工作约束；guideline 的强制规范集中在 `references/rules/`。实际使用时：

1. 先读取当前触发的 Skill 入口。
2. 选择一个最相关的 Primary Reference；实现或审查页面时，同时读取 `references/rules/design-system.md` 的适用范围、例外和主题路由，继续读取本次任务涉及的视觉与主题规范章节。
3. 只读取该文档中解决当前问题的标题、表格或合同卡片。
4. 只有当前问题仍缺少证据时，才继续打开下一个 Reference。
5. 组件 API、页面组合、Example 证据和目标项目编译结果始终分开记录。

不应把整个 references/ 目录预加载成百科全书。

## 证据范围

- 当前开发基线：发布标签 `v0.9.5-beta01`，提交 `6d2554a850b8960e2616c968263ff64beb5ffb03`
- 上游仓库：[compose-miuix-ui/miuix](https://github.com/compose-miuix-ui/miuix)
- 当前源码文档源：[Miuix 0.9.5-beta01 docs](https://github.com/compose-miuix-ui/miuix/tree/6d2554a850b8960e2616c968263ff64beb5ffb03/docs)

源码检查、目标编译、设备效果分别记录。确认目标实际解析到 beta01，并核对本地替换或修改；固定版本合同不能直接当成目标项目已验证 API。其他 Miuix 版本不在本仓库维护范围内。
