# librime-macos-prebuilder

本仓库用于在 **macOS 平台上对 librime 进行 rolling build（滚动构建、仅服务于Fcitx5 macOS的fcitx5-rime插件）**。

仓库本身不包含任何源代码，仅作为一个 **CI-only 仓库**，通过 GitHub Actions 自动拉取并复用  
`fcitx-contrib/fcitx5-prebuilder` 的构建体系，对 `rime/librime` 进行持续编译。

---

## Rolling Build 机制

本仓库的 CI 并非无条件重复构建，而是采用 **基于 commit 的 rolling build 策略**：

1. CI 会记录**最近一次成功构建时使用的 librime commit ID**
2. 在新一轮 CI 触发时：
   - 获取 `rime/librime` 仓库当前的 `HEAD` commit
   - 将其与最近一次已构建的 commit ID 进行对比
3. 仅当检测到：
   - `HEAD` commit **发生变化**
   
   才会触发新的 librime 构建流程
4. 若 commit 未发生变化，则跳过构建步骤

该机制用于避免无意义的重复编译，同时保证构建结果始终跟随 librime 上游最新提交。

---

## 仓库用途

- 提供一个 **自动化的 macOS librime rolling build**
- 持续跟踪 `rime/librime` 上游变更
- 生成可用于测试或开发的最新 librime 构建产物
- 不直接参与 fcitx5-mac 或其插件开发

---

## 构建体系来源

本仓库 **不自行维护构建脚本或依赖定义**，而是直接复用：

- [`fcitx-contrib/fcitx5-prebuilder`](https://github.com/fcitx-contrib/fcitx5-prebuilder)

该仓库已完整解决 macOS 平台上的 toolchain、依赖及构建细节问题。本仓库仅在 CI 中调用其既有能力。

---

## CI 触发方式

GitHub Actions 支持以下触发方式：

- 定时触发（`schedule`）
- 手动触发（`workflow_dispatch`）

CI 定义位于：`.github/workflows/build.yml`



---

## 构建产物

- 构建产物由 GitHub Actions 生成
- 以 artifact 或 release 的形式提供
- 每份产物都记录一个明确的 librime commit到release log
- 仅用于测试、验证或个人开发用途

---

## 设计原则

- **Rolling build，而非 nightly blind build**
- **最小侵入**：不 fork、不修改上游仓库
- **职责解耦**：
  - `fcitx-contrib/fcitx5-prebuilder`：服务 fcitx-contrib/fcitx5-plugins 生态
  - `librime-macos-prebuilder`：专注 librime 的持续构建
- **可追溯性**：构建结果与上游 commit 一一对应

---

## 适用场景

- 需要在 macOS 上：
  - 跟踪 librime 上游最新改动
  - 验证某个提交是否引入行为变化
- 希望避免重复配置复杂 macOS 构建环境的开发者

---

## 许可说明

本仓库不包含任何源代码，仅包含 CI 配置。

librime 及其依赖的许可证请分别参考其上游项目。


