# Desktop Web 插件导入设计

## 目标

在 Desktop 插件页提供显式操作，将 Web profile 已安装的第三方 bundle 安装到 Desktop profile，并保留其启用状态。

## 范围

- 读取 `$DSH_HOME/profiles/web` 的依赖和 bundle 选择，不修改 Web profile。
- 排除 Desktop 已安装的 bundle，以及由当前 dsh runtime 提供的内置 bundle。
- 用户选择导入项后，通过现有 Plugin Manager 在 Desktop profile 安装，沿用兼容性检查、日志、取消和构建脚本审批。
- Web 中禁用的 bundle 在 Desktop 中安装为禁用；Desktop 已有插件和配置保持优先。
- 不迁移 Web profile 的 `cordis.patch.yml`，不迁移其 Web 专属配置。

## 设计

桌面插件页增加 Web 插件导入入口。Host 提供 Web profile 可导入 bundle 清单，包含包标识、版本/安装规格和启用状态；清单读取失败时返回可操作错误，不阻断 Desktop 启动或现有插件管理。

用户确认导入后，Client 复用 Plugin Manager 安装操作逐个安装所选 bundle。每个 bundle 单独结算并显示成功或失败；失败项可重试，不回滚已成功的其它导入。禁用项安装后保持未启用。再次打开导入入口时，已在 Desktop 安装的 bundle 不再列为可导入项。

Desktop 启动不运行 pnpm。所有安装仍由用户从插件页发起，并使用 Desktop profile 的包管理器配置与审批机制。

## 验收

- Web profile 的有效第三方 bundle 出现在 Desktop 导入清单中，内置和已安装项不出现。
- 导入后包位于 Desktop profile；Web profile 的 manifest、patch 与安装文件不变。
- Web 中禁用的 bundle 在 Desktop 安装后仍禁用。
- 重复导入不重复安装；部分失败不影响成功项，失败信息支持重试。
- 插件清单读取错误不会改变普通 Desktop 插件管理行为。

## 验证

覆盖清单筛选、启用状态、目标 profile 安装、重复导入、部分失败和 Web profile 不变；运行插件管理器与桌面 GUI 的针对性测试。
