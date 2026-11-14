# Miuix Skills

本仓库维护两类相互配合、但职责独立的 Miuix Skill：一个回答“组件库实际有什么、怎么调用”，另一个回答“界面应该如何组织和使用”。本仓库本身是维护用的 monorepo，不是第三个 Skill。

## 两个 Skill

| Skill | 负责的问题 | 入口 |
|---|---|---|
| `miuix-components` | 组件能力、真实 API、导入和模块、默认值、宿主与生命周期、版本差异、源码核验 | [`miuix-components/SKILL.md`](./miuix-components/SKILL.md) |
| `miuix-guidelines` | 页面骨架、所有权、组件语义选择、层级、间距、状态、Insets、自适应和界面审查 | [`miuix-guidelines/SKILL.md`](./miuix-guidelines/SKILL.md) |

## 选择规则

- 询问实际签名、参数、依赖、版本、编译错误或组件行为时，使用 `miuix-components`。
- 新建页面、调整页面结构、改善视觉层级或审查现有界面时，使用 `miuix-guidelines`。
- 同时涉及设计和实现时，让 `miuix-guidelines` 决定页面组合，只把实际调用到的具体符号交给 `miuix-components` 核验。
- 两个 Skill 都按自己的 Reference 路由工作，不要求预加载另一个 Skill 的全部文档。

## 内容归属

### `miuix-components`

- [`api-contracts.md`](./miuix-components/references/api-contracts.md)：固定版本中已核对的高频公开 API。
- [`library-map.md`](./miuix-components/references/library-map.md)：固定源码中各模块和 commonMain 区域的导览与路由。
- [`component-selection.md`](./miuix-components/references/component-selection.md)：公共组件能力和组件家族查找。
- [`source-verification.md`](./miuix-components/references/source-verification.md)：固定源码、文档、Demo 和 Example 的核验边界。
- `miuix-nav`、Overlay/Window、Preference、主题设置、颜色 Token、视觉效果和版本迁移等库事实。

### `miuix-guidelines`

- [`page-structure.md`](./miuix-guidelines/references/page-structure.md)：页面宿主、主内容、Insets、状态和瞬态层的所有权。
- [`page-compositions.md`](./miuix-guidelines/references/page-compositions.md)：Scaffold、顶栏、底栏、悬浮导航、浮动操作、滚动内容和 Overlay 的组合配方。
- [`design-language.md`](./miuix-guidelines/references/design-language.md)：层级、排版、分组、颜色语义、加载和失败状态。
- [`usage-patterns.md`](./miuix-guidelines/references/usage-patterns.md)：仅在需要集成组合证据时读取的 Example 模式。
- [`ui-review-workflow.md`](./miuix-guidelines/references/ui-review-workflow.md)：现有页面的诊断、改进和验证。

## 共同证据版本

两个 Skill 当前都以 Miuix `v0.9.4` 为基准，源码证据固定在提交 `39c40f99844227b853f0049a0933b1f3ae6c00ba`。版本迁移和具体 API 事实由 `miuix-components` 负责；页面规范不会把某个组件或 Example 页面宣称为所有页面的强制模板。

## 安装

每个子目录都是一个独立 Skill 根目录，必须让使用环境分别看到其中的 `SKILL.md`。如果宿主不支持从 monorepo 直接发现嵌套 Skill，请分别复制或链接以下两个目录到 Skill 目录：

```text
miuix-components/
miuix-guidelines/
```

具体使用哪一个 Skill，由任务是“核对组件库事实”还是“设计/审查界面”决定；两者同时需要时也只读取各自当前任务相关的 Reference。
