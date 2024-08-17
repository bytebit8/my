## Changesets 是什么？

A tool to manage versioning and changelogs with a focus on multi-package repositories。

一款专注于 `Monorepo` 仓库的“版本提升”与“日志变更”的工具。

> 多包存储库（multi-package repositories） MonoRepo 的称呼方式之一。

因此，记住 `Changesets` 的两大核心功能：

- [*] 版本提升：修改 package.json 的 version 字段等。
- [*] 日志变更：创建 CHANGELOG.md 文件并追加变更日志。

“变更日志”：有关分支或提交中所做出更改的信息说明。

## Changesets 的背后理念

按照官方文档中给出的有关 `Changesets` 背后的理念，它主要是为了解决以下两个问题被设计出来的：
1. 在 Git 中不能很方便的记录详细的变更日志。
2. 记录变更日志的最佳时机是在提交 `PR` 时（此时记忆犹新），而不是在最后的发版时刻。

对此，`Changesets` 提出了“变更集”文件的方案。变更集文件记录了有关变更的日志信息和版本控制信息。因 Git 存储库限制，变更集文件只能存储在本地文件系统中，随同 `PR` 一起提交到远程存储库，最后在恰当的时机去消耗这些变更集文件，从而实现“版本提升”与“日志变更”的功能。

## Changeset files 是什么？

### 变更集文件是什么？

一个带有 YAML 前置数据的 Markdown 文件，它主要包含两部分内容：
* **前置数据**：可以理解为变更集文件的元信息，包含了受影响（变更）的包以及意图提升的版本类型。
* **内容摘要**：也就是变更的具体日志信息。

```markdown
---
"@myproject/cli": major 
"@myproject/core": minor
"@myproject/main": patch
---
Change all the things
```

>[!tip]
>“变更集文件”的文件名是由 `@changesets/cli` 生成的具有随机，人类可读的名称，以避免在生成它们时发生冲突。

### 变更集文件的存放位置

通常保存在项目根目录 `.changeset` 隐藏文件夹中。
```
.changeset
├── UNIQUE_ID.md
└── config.json
```

### 变更集文件是可堆叠的

可以将任意数量的变更集合并成一个发布，`Changesets` 会压平每个包的版本提升类型（基于每个包的最大版本提升）。

> * 多个 `patch` 合并成一个 `patch` 提升。
> * 多个 `patch`，一个 `minor`，合并为一个 `minor` 提升。
> * 多个 `patch`，多个 `minor`，一个 `major`，合并为一个 `major` 提升。

### 消耗变更集文件

在恰当的时机，执行 `changeset version` 命令，组合所有变更集文件，为每个包创建一个版本提升（基于每个包的最大版本提升），并在每个包的根目录下创建 `CHANGELOG.md` 文件以追加的方式写入变更的日志（内容摘要）。并在需要时更新依赖关系

变更集文件一旦被消耗后，所有变更集文件都将被删除。每个变更集文件只会被使用一次。

当变更集文件被消耗时，@changesets/cli 会根据



## changesets 的工作流程

“变更集”文件是具有声明意图的文件，只要在消耗变更集文件时，才会实际进行版本提升与日志变更。因此这种设计，很容易的在围绕“变更集”文件时分为两种角色：
* 变更集文件的生产者：对应的是项目的 Deveolper
* 变更集文件的消费者：对应的是项目的 Maintainer

项目的各个角色再使用Changesets 时，流程如下图所示：

## Changesets 配置


## Changesets Bot


## 常见问题

### 什么时候需要添加多个变更集文件？

- 您想要发布具有不同更新日志条目的多个软件包 —— 更新了多个包。
- 您对包进行了多项更改，每项更改均应单独调用 —— 做了多次不同的更改。

### 如何编写变更集文件？

1. WHAT the change is  （变更的内容是什么？）
2. WHY the change was made  （变更的原因）
3. HOW a consumer should update their code （消费者如何更新他们的代码）
