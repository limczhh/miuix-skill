# Miuix Skills

本仓库是 Miuix Compose UI 的开发参考，提供两个职责分开、可分别触发的 Skill，作为同一插件成套分发：

- **miuix-components（路书）**：主动读取固定版本官方文档，解决组件选择、API、模块与宿主问题。组件介绍、完整参数表和常规示例交给官方文档；本地只维护有实际价值的接入差异、易错点和必要的组合示例，有疑义时再检查源码。
- **miuix-guidelines（准则）**：回答页面“如何组织骨架、所有权、层级、Insets 和组件组合”。作为设计规范，严格区分 **强制性红线规范（rules）** 与 **非强制性启发指南（guides/workflows）**。

仓库根目录只是源码仓库和维护说明，不是第三个 Skill。两个 Skill **仅服务 Miuix `0.9.5-rc02`**，固定标签 `v0.9.5-rc02`、提交 **e636bd6c07f14d588fe084f0c3a5b57eafe20230**。依赖示例使用完整 rc02 版本号；不维护旧版本 API 或兼容分支，仅保留一份 rc01→rc02 升级差异（附 beta03 跳升要点）。目标依赖不符时说明范围差异，依赖升级仍按用户授权执行。

## 当前版本与 Glass

- [依赖、主题与平台设置](./miuix-components/references/setup-and-theme.md)：rc02 模块选择、Android API 24/33 与 compileSdk 37.2 边界、主题/指示器与色彩速查。
- [RC01 → RC02 升级差异](./miuix-components/references/rc02-migration.md)：`GlassBottomSheet` `progressiveBlur = false` 默认切换、`GlassScrollableTabRow`、`GlassTopAppBar` 渐进参数、`rememberLayerBackdrop(backgroundColor, onDrawUnderlay)`、`ScrollBar(interactive)`、弹层安全区截断与 `rememberListPopupLayoutInfo` 内部化。
- [Glass 替换与共存](./miuix-guidelines/references/glass-adoption.md)：旧组件对应关系、仅替换 Overlay 表面、完整组件迁移、普通与 Glass 两套外观共享状态。
- [Glass 组件与模糊降级](./miuix-components/references/glass-and-blur.md)：22 个 Glass 组件接入契约、`GlassCard`、`GlassBottomSheet` 渐进头部，以及无全局开关下的组件降级矩阵。

普通组件与 Glass 家族都使用 rc02。Glass 可选，运行时效果降级仍需保留；组件替换时遵循强制规范中的采样、交互、状态和宿主要求。文档只保留当前用法，过时内容直接修订或删除，避免追加成多版本百科。

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

新应用如果包含几个顶层页面和底部/悬浮导航，先读取 [应用壳与导航组合规范](./miuix-guidelines/references/app-shell-and-navigation.md)。它先固定 Pager、导航栏、路由和透明背景的边界，再进入具体页面和组件选择。

## Skill 的实际流程

### 组件库流程

1. 确认目标实际解析到 `0.9.5-rc02`，以及相关平台、宿主和本地替换；构建输入不变时复用这个结论。
2. 直接按任务领域从 [miuix-components/SKILL.md](./miuix-components/SKILL.md#direct-routes) 一跳打开对应专题参考；未知组件或模块归属时查 [组件总目录](./miuix-components/references/catalog.md)。
3. 标准 rc02 的已覆盖调用复用合同，标记 `catalog-verified`；未覆盖、源码被替换或行为仍不明确时，才按 [源码核验](./miuix-components/references/source-verification.md#api-verification-gate) 调查具体缺口。
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

标准刷新页由列表统一持有顶栏滚动连接。公共边距按各边所需安全距离合并，并明确侧栏已占用的起始侧，不要求复制完整的 Example Insets 架构。普通滚动联动与 rc02 回弹视觉通道分别核验，不添加反射或重复控制器。弹层保留到退出结束，关闭中的按下态与新打开会话的草稿分别处理；搜索恢复查询和稳定展开状态。

规范以本套设计系统自身的条款表述。项目代码用于核对实际写法，示例用于解释组合；二者都不意味着把专用 helper、业务名词、偶发写法或某个版本的 API 当成通用要求。

`references/rules/` 是 UI 强制条款的唯一维护位置。其余 reference 保留流程、决策方法、诊断和示例，通过链接应用规范；`miuix-components` 负责从官方文档和源码解析 API，不另行维护一套完整 API 手册。

1. 先确认已有主题、路由 owner、页面边界、页面 host、平台和 source set。新应用若包含多个顶层页面与底部/悬浮导航，先读 [应用壳与导航组合规范](./miuix-guidelines/references/app-shell-and-navigation.md) 并固定一套壳层架构。
2. 新页面或结构改动先明确页面骨架与所有权；局部修改只检查受影响的 owner、调用方及条款，不强制填写整页工作表。
3. 再按语义选择组件；页面是设置页、列表页还是表单页，不会自动决定组件。
4. 只把最终确实要调用的 Miuix 符号交给 **miuix-components** 核对。
5. 根据已核对的 API 写组合代码，保留目标项目已有的行为和状态归属。
6. 检查适用规范是否满足，说明有理由的偏离与尚未解决项；编译后再分别检查预览、渲染、设备、自适应、Insets、无障碍和状态变化。规范符合情况、API 核验、编译和视觉验证分别报告。

两者组合时，边界是：

```text
miuix-guidelines：页面骨架和组合关系
        ↓
miuix-components：读取固定版本官方文档，应用接入注意事项，按需核对源码
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
├── miuix-components/                         # API、模块、宿主、降级与版本真相源（11 文件）
│   ├── SKILL.md                              # 唯一版本 Pin / URL 解析规则与一跳直达路由
│   └── references/
│       ├── catalog.md                        # 模块边界、语义线索与全量组件/Doc/源码总表
│       ├── setup-and-theme.md                # 依赖、SDK 边界、Theme/Scaffold 与视觉色彩速查
│       ├── controls-and-menus.md             # 按钮、输入、控件与 Preference/Menu 契约
│       ├── bars-scroll-and-navigation.md     # TopAppBar、Refresh、Overscroll、Tab/Pager、导航条
│       ├── overlays-and-windows.md           # Overlay* vs Window*、Motion、桌面 miuix-window
│       ├── glass-and-blur.md                 # Glass 22 组件、Backdrop 录制与组件降级矩阵
│       ├── effects-and-icons.md              # 自定义 Blur、EdgeStroke/TiltLight、Squircle 与图标
│       ├── miuix-nav.md                      # NavDisplay、路由序列化与 Expansion 展开转场
│       ├── rc02-migration.md                 # rc01 → rc02 升级差异（含 beta03 要点）
│       └── source-verification.md            # 源码升级路径、证据标签与 Example 地图
└── miuix-guidelines/                         # 页面骨架、所有权与习惯规范（9 文件）
    ├── SKILL.md                              # 边界声明、适用范围与一跳路由（不含具体 API 版本锁）
    └── references/
        ├── workflows.md                      # 决策顺序、所有权勘察、单页/旧页改造与 UI 审查
        ├── app-shell-and-navigation.md       # 多页应用壳骨架、SelectionCoordinator 与 Pager 协同
        ├── layout-patterns.md                # 顶栏状态栏延伸、手势诊断与单活跃弹窗编排
        ├── glass-adoption.md                 # Glass 场景录制层次、迁移映射与双外观共存策略
        └── rules/                            # 强制性设计红线与习惯数值
            ├── design-system.md
            ├── page-shell.md
            ├── settings-and-overlays.md
            └── state-and-performance.md
```

每个 `miuix-*` 子目录都有独立的 Skill 入口，两个 Skill 之间**零跨目录相对链接**、职责解耦：`.codex-plugin/` 和 `.agents/plugins/` 将二者成套分发，使用时按需求单独触发或串联配合即可。

## 内容维护

维护原则：**单一事实源 + 官方文档负责常规手册 + 本地仅留边界与坑点 + `rules/` 定义设计习惯与红线。** 固定 Commit Hash 与 GitHub Raw URL 模板只在 [miuix-components/SKILL.md](./miuix-components/SKILL.md#documentation-first-resolution) 集中声明一次，正文内部均使用仓库相对路径，避免版本号更新时波及几十个文件；`miuix-guidelines` 不绑定 Miuix 具体版本号与完整 API 声明，具体符号交由 `miuix-components` 核验。

常规开发由代理主动读取所选组件的固定提交文档，而不是只要求用户自行阅读。优先复用已经读取的同版本证据；远程不可用时读取匹配的本地 checkout 或附加源码。缺少证据的调用明确留待核对，不切换到 `main` 或猜测参数。具体流程见 [源码核验](./miuix-components/references/source-verification.md#api-verification-gate)。

收录本地内容时，先判断它是否改变实际开发决策：官方文档已充分解释的内容改为链接；官方文档遗漏或含糊的行为保留源码依据；页面风格和应用层约束写入适用范围明确的 rules。组合示例保留跨组件的所有权或生命周期关系，删除普通入门示例。发现错误时直接修订正文和关联示例。

发布审查围绕有限的代表任务：普通组件接入、顶栏与系统区域、刷新与滚动、两页/多页导航、弹层退出生命周期、普通/Glass 切换及模糊降级。每项能从入口找到正确文档和适用规则，并说明未验证边界；不把补齐整座组件库当作无限延期的条件。示例涉及的目标构建和设备交互仍分别验证，不用文档走查替代它们。

声明匹配、组件名称齐全或文档链接通过，只能证明各自检查的范围。不能据此宣布内容全面正确；源码不能证明的视觉、手感和平台行为应明确保留实测边界。

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

- 当前开发基线：发布标签 `v0.9.5-rc02`，提交 `e636bd6c07f14d588fe084f0c3a5b57eafe20230`
- 上游仓库：[compose-miuix-ui/miuix](https://github.com/compose-miuix-ui/miuix)
- 当前源码文档源：[Miuix 0.9.5-rc02 docs](https://github.com/compose-miuix-ui/miuix/tree/e636bd6c07f14d588fe084f0c3a5b57eafe20230/docs)

源码检查、目标编译、设备效果分别记录。确认目标实际解析到 rc02，并核对本地替换或修改；固定版本合同不能直接当成目标项目已验证 API。其他 Miuix 版本不在本仓库维护范围内。
