# Miuix Skill

Miuix Compose UI 组件库的开发参考与工作指南。

Miuix 是一个独立的 Compose UI 组件库，可用于实现具有小米设计风格的界面，但不等同于小米官方 HyperOS UI 组件库。本 Skill 整理了 Miuix 的组件、页面结构、主题、状态、弹窗、导航和源码核验之间的关系，为界面设计和代码实现提供统一参考。

## 使用场景

- 新建设置页、搜索页、表单页或多页面应用，但尚未确定页面结构。
- 优化现有 Compose 页面，使其更符合 Miuix 的层级、间距和交互规范。
- 判断 `OverlayDialog` 与 `WindowDialog` 的适用场景，或组合 Preference、菜单和导航组件。
- 核对组件的实际参数、默认行为、主题依赖或版本变化。
- 参考 Miuix Example 的完整页面，将其整理为适合目标项目的实现方式。

## 使用方式

无需预先记忆组件名称或参数，直接描述目标界面即可：

```text
我想做一个带底部导航、设置页和确认弹窗的 Miuix 风格页面，应该如何组织？
```

也可以从更具体的问题开始：

```text
用 Miuix 创建一个包含开关和下拉选择的设置页。
```

```text
OverlayDialog 和 WindowDialog 有什么区别？请给出使用建议。
```

```text
把这个 Compose 页面改成 Miuix 风格，并核对实际 API。
```

## 输出内容

根据任务范围，输出内容可以是：

- 一个带状态和回调的最小组件调用，明确它需要什么祖先组件。
- 一个可以接入现有项目的完整页面，包含状态归属、主题和导航假设。
- 一个独立运行的 `App` 示例，包含依赖、主题和宿主结构。
- 一份现有 UI 的改进方案，说明层级、间距、状态、Insets、自适应和无障碍问题。
- 一组经过文档、Demo、Example 和源码核对的 API 结论，以及清楚区分的编译和视觉验证结果。

## 内容从哪里来

遇到具体组件时，可以从文档了解用途，从 Demo 看到最小调用，从 Example 看到完整组合，再回到对应版本的源码确认参数和行为。这样既能保持 Miuix 的设计语言，也不会把 Example 中的应用代码误当成公共 API。

当前目录中的 Reference 不需要从头读完；它们按主题组织，方便在需要时展开，也方便维护者定位证据：

### 页面与工作流

- [`ui-review-workflow.md`](./references/ui-review-workflow.md)：已有页面的诊断、改进和结果报告。
- [`usage-patterns.md`](./references/usage-patterns.md)：从 Example 提炼的页面、设置、导航、弹窗和状态模式。

### 设计与组件

- [`design-language.md`](./references/design-language.md)：颜色、文字、层级、间距、Defaults 和错误/加载状态。
- [`component-selection.md`](./references/component-selection.md)：场景地图和基础组件目录。
- [`overlays-and-windows.md`](./references/overlays-and-windows.md)：Overlay、Window、弹窗和返回行为。
- [`preferences-and-menus.md`](./references/preferences-and-menus.md)：Preference、Dropdown 和菜单组件。
- [`styling-icons-and-effects.md`](./references/styling-icons-and-effects.md)：图标、模糊、Squircle 和视觉效果。

### 配置与版本证据

- [`setup-and-theme.md`](./references/setup-and-theme.md)：依赖、`ThemeController`、`MiuixTheme`、Preview 和根宿主。
- [`source-verification.md`](./references/source-verification.md)：文档、Demo、Example 和源码路径。
- [`release-v0.9.3.md`](./references/release-v0.9.3.md)：版本迁移时需要重新核对的 API。

## 安装

### Codex

全局安装：

```bash
git clone https://github.com/limczhh/miuix-skill.git ~/.codex/skills/miuix
```

项目内安装：

```bash
git clone https://github.com/limczhh/miuix-skill.git .codex/skills/miuix
```

### Claude Code

全局安装：

```bash
git clone https://github.com/limczhh/miuix-skill.git ~/.claude/skills/miuix
```

项目内安装：

```bash
git clone https://github.com/limczhh/miuix-skill.git .claude/skills/miuix
```

其他工具请将仓库放入对应的 Skill 目录，并确保 `SKILL.md` 位于目录根部。

## 范围与版本

这是一套专注于 Miuix Compose UI 的开发参考。它可以帮助你构建、完善、审查和重构 Miuix 界面；当 Miuix 没有对应组件时，会优先考虑 Compose 基础能力，并明确说明自定义行为，不会为了套用 Miuix 而强行替换项目中已有的其他 UI 体系。

每个版本都会对应一组可复核的 API 证据。当前仓库的证据快照基于 Miuix `v0.9.3`，这不是永久的版本限制；随着 Miuix 上游更新，组件目录、Reference、源码路径和迁移说明也会一起更新。

具体的视觉效果和设备行为，仍取决于目标项目、平台以及可用的编译、预览或设备环境。

当前证据入口：

- [Miuix GitHub](https://github.com/compose-miuix-ui/miuix)
- [Miuix v0.9.3 文档源文件](https://github.com/compose-miuix-ui/miuix/tree/v0.9.3/docs)

## 贡献

欢迎补充组件用法、修正文档和完善 API 参考。涉及具体 API 时，请附上对应的文档、Demo、Example 或源码路径，并说明验证所依据的 Miuix 版本。
