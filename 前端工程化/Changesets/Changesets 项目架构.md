## Action

一款用于 GitHub Action 的程序，可以自动创建拉取请求，更新包版本和变更日志并在合并 PR 后自动发布包到远程仓库。
[@changesets/action](https://github.com/changesets/action)

对于 GitLab 来说，推荐用下面这个包（非官方）：
[changesets-gitlab](https://github.com/un-ts/changesets-gitlab)

## Bot

一款专门用于 GitHub 的 Bot 程序，可以检测 PR 中的变更集文件。
其功能与 action 相同，只是更加的自动化。

## Changesets

一个 `Private` 的 Monorepo，包含如下关键的子包：

### cli

Changesets 的核心实现。可以创建变更集文件、提升版本、记录日志、发布变更。

### changelog-git

为 Git 仓库生成专属的日志信息格式。

### changelog-github

为 GitHub 仓库生成专属的日志信息格式
### write

使用 `human-id` 生成 changeset file。

### config

读取与解析 `.changeset/config.md` 文件的配置。

### parse

解析变更集文件。

### assemble-release-plan

读取 changeset 文件然后分析出需要更新的包版本以及其依赖关系

### apply-release-plan

接收来自 `assemble-release-plan` 的计算结果，然后实际的修改包的版本、删除changeset 文件、创建活更新 CHANGELOG.md 文件。

### pre

进入或退出 `pre` 模式

### git

收集了 Changesets 常用的 git 操作方法。

### types

Changesets 各个包所需的 TypeScript 类型声明。