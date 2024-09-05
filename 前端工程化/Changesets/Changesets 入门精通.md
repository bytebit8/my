## Changesets 是什么？

官方说明：

	A tool to manage versioning and changelogs with a focus on multi-
	package repositories。
	......
	publishing new versions of packages based on the provided information.

归纳总结：

**一款专注于 `Monorepo` 仓库的“版本提升”与“日志变更”的工具。也是 Monorepo 场景下常用的发包工具。**

核心功能：

- [*] 版本提升：会修改 `package.json`文件的 `version` 字段。
- [*] 日志变更：“包”根目录创建 `CHANGELOG.md` 文件并追加日志详情。
- [*] 发布变更：自动识别“包”是否存在新的版本需要发布。

>[!question]
>**“变更日志”**：在分支或提交中所做出更改的信息说明。
>**“Multi-Package Repositories”**: 多包存储库，Monorepo 的称呼方式之一。
>

而能让 `Changesets` 做出以上两个行为的前提就是创建“变更集文件（changeset）”。

## Changeset 是什么？

“变更集”文件。一个带有 YAML 前言的 Markdown 文件，它主要包含两部分内容：
* **前言数据**：变更集文件的元信息。包含了受影响的包以及意图提升的版本类型。
* **内容摘要**：具体的“变更日志”。
```markdown
---
"@myproject/cli": major 
"@myproject/core": minor
"@myproject/main": patch
---
Change all the things
```
^e097f9

“变更集文件”存放在项目根目录 `.changeset` 的隐藏件夹中：
```
.changeset
├── UNIQUE_ID.md
└── config.json
```

## 快速指南

### 安装 changesets/cli

```bash
npm i @changesets/cli --save-dev
```

### 初始化项目

在 `package.json` 文件的 `scripts` 字段，添加以下命令：
```json
"scripts":{
	"changeset":"changeset"
}
```

执行命令初始化项目：
```shell
npm run changeset init
```

`init` 命令执行完成后，会在项目的根目录下创建一个`.changeset` 目录，并自动创建一个 `config.json` 文件。命令的执行原理非常简单，就是用 `fs` 库将配置文件以及目录结构写入到项目根目录下。

### 创建变更集文件

执行 `npm run changeset` 或 `npm run changeset add` 命令可以打开交互式命令行来创建变更集文件。主要经过下面三个子步骤：

**1. Which Packages**
	首先，选择要更变更的包，Changesets 底层通过 `@manpkg/get-packages` 包来构建出 Monorepo 中所有子包的依赖图，并使用 `git diff` 命令获取获取本次所有 commit 涉及到的存在变更的包，不过即使没有修改的包，用户也能选择。

**2. Bump Version**
	紧接着开始选择要变更的版本类型，Changesets 底层通过 `semver` 包来实现语义化版本控制。主版本(major)、次版本(minor)、补丁版本(patch)。

**3. Enter Summary**
	最后，填入 `Summary` 也就是我们变更的摘要内容，最终就会生成一个名称随机但人类可读的 changeset 文件了。文件格式如：[[#^e097f9]] 所示。
	“变更集”文件名是由底层的 `human-id` 包生成的具有随机，人类可读的名称，以避免在生成它们时发生冲突。

下面是命令完整的交互过程：
``` shell
➜  changesets-test git:(master) ✗ npm run changeset
> changesets-test@1.0.0 changeset

🦋  Which packages would you like to include? · plugin, core, pkg
🦋  Which packages should have a major bump? · No items were selected
🦋  Which packages should have a minor bump? · plugin, core, pkg
🦋  Please enter a summary for this change (this will be in the changelogs).
🦋    (submit empty line to open external editor)
🦋  Summary · write change summary
🦋  
🦋  === Summary of changesets ===
🦋  minor:  plugin, core, pkg
🦋  
🦋  Note: All dependents of these packages that will be incompatible with
🦋  the new version will be patch bumped when this changeset is applied.
🦋  
🦋  Is this your desired changeset? (Y/n) › true
```

>[!tip]
>也可以完全手动创建 changeset 文件，这是被允许的。

### 消耗变更集文件

创建变更集文件不会执行版本变更与日志记录的行为，只有在消耗变更集文件时，才会为每个受影响的包进行**版本提升**（基于每个包的最大版本提升），并且还会在每个包的根目录下创建（如果不存在）一个 **CHANGELOG.md** 的文件，以追加的方式写入变更的日志（摘要内容），并在需要的时候同时更新依赖的版本。

执行下面的命令，便可以消耗所有变更集文件：

```shell
npm run changeset version
```

变更集文件一旦消耗就会被删除，因此每个变更集文件只会被使用一次。

![[changeset-version.svg]]

### 发布变更

执行下面命令，Changesets 会对比包的本地版本与 registry 上的版本，一旦 registry 上没有，就会调用包管理的 `publish` 命令发布包；如果已经存在，就不会再发布包了。
```shell
npm run changeset publish
```

### 组合变更集文件

变更集文件是可堆叠的，可以将任意数量的变更集合并为一个发布，`Changesets` 会压平每个包的版本提升类型（每个版本类型只会被提升一次）。

* 多个 `patch` 合并成一个 `patch` 提升。
* 多个 `patch`，一个 `minor`，合并为一个 `minor`。
* 多个 `patch`，多个 `minor`，一个 `major`，合并为一个 `major` 提升。
* 多个 `major` 合并为一个 `major`。

## 依赖变更

>[!warning]
>以下行为仅作用于 **Monorepo** 仓库中。只有在单仓中才会变更具有依赖关系的子包。

Changesets 有关具有依赖关系包的变更策略如下：

**“依赖者包”与“依赖包”的定义！**
如果 A 包依赖了 B 包：
- 对于 A 包而言，B 包是它的 —— “依赖包”。
- 对于 B 包而言，A 包是它的 —— “依赖者包”
用“发布/订阅模式”去理解：
- B 包是提供者。
- A 包则是消费者。

**什么场景下 Changesets 会自动变更“依赖者包”？**
1. 当单仓的**某个被依赖包**存在重大的版本变更时，Changesets 会检查依赖它的其它包是否存在超出语义版本控制范围（也就是不能匹配到所依赖包的最新版本）的情况，如果存在，Changesets 会以 `patch` 的方式自动更新所有依赖者包，并将依赖更新日志写入到依赖者包的 CHANGELOG.md 文件中。
	例如 ：`pkg-a` 依赖了 `pkg-b:^1.0.0`，当 `pkg-b` 产生 bump major 时，Changesets 会以 `patch` 的方式自动更新 `pkg-a` 包。

2. 当单仓同时存在**多个变更包**时，对于具有依赖关系的包，会为依赖者包额外增加一个 `patch` 级别的版本变更，同时将依赖更新日志写入到依赖者包的 CHANGELOG.md 文件中。
	例如 ：`pkg-a` 依赖了 `pkg-b`，当同时变更了 `pkg-a` 与 `pkg-b` 时，会为 `pkg-a` 额外追加一个 `patch` 并生成依赖的更新日志。 ^a005e5


**为什么当依赖包超出语义版本控制范围时需要强行对“依赖者包”进行变更？**
	因为在 Monorepo 中具有依赖关系的多个子包，其底层都是以相对位置引入的，如果依赖者包的语义版本范围无法匹配到被依赖包的最新版本，那么“包管理器”就可能会拒绝被依赖包的载入，因此就必须要在此种情况下对依赖者包进行自动检查并更新，以确保依赖关系的正确性。

**能否忽略 Changesets 的自动检查和更新机制？**
	有的。
	对于 npm 可以将依赖包的版本控制范围修改为：`pkg-b:*`。
	对于 pnpm 则可以修改为：`pkg-b:workspace:*` 。

## 背后理念

按照官方文档中给出的有关 `Changesets` 背后的理念，它主要是为了解决以下两个问题被设计出来的：
1. 在 Git 中不能很方便的记录详细的变更日志。
2. 记录日志的最佳时机是在提交 `PR` 时（此时记忆犹新），而不是在最终发版时。

对此，`Changesets` 提出了创建“变更集”文件的方案。变更集文件记录了有关变更的日志信息和版本类型。通过将“变更集”文件存储在本地文件系统，以解决 Git 存储库的限制。在提交产物时，随同 `PR` 一起提交到远程存储库，最后在恰当的时机去消耗这些变更集文件，从而实现“版本提升”和“日志变更”的操作。

变更集文件的版本类型遵循了语义化版本控制([semver](https://semver.org/lang/zh-CN/))的标准，对于标准发行版：
* `major` ：主版本
* `minor` ：次版本
* `patch` ：补丁版本

对于“预发行版”（也叫先行版本），以上版本类型依然适用。但需要专门的命令选项进入/退出“预发行模式”。

## 工作流程

`Changests` 的工作流程很简单：

1. 每次变更都使用 `changeset add` 创建变更集文件。
2. 准备发布时运行 `changeset version` 消耗变更集文件。
3. 验证无误后,将变更合并到主线分支。
4. 最后执行 `changeset publish` 发布变更的包。

大致步骤，如下图所示：

![[changesets-steps.svg]]

但是，要要理解 Changesets 工作流程中蕴含的真实意图，则必须要将变更集文件的创建与消耗区分开来看。“变更集”文件一种是含有变更意图的文件，它包含了两部分内容：“要提升版本的类型”和“要记录的变更信息”。只有消耗变更集文件，才会实际进行版本提升和日志记录。因此，这种设计很容易围绕“变更集”文件，将变更流程分派给不同职责的角色去完成：
* `Developer` 在变更时创建变更集文件，并随同 `PR` 一起提交。
* `Maintainer` 在适合的时机消耗变更集文件，确认无误后，发布变更版本。

通常而言，后两个步骤可交由 CI 完成，项目的 Maintainer 只需要完成审批即可。下面是按职责区分的 Changesets 工作流程：

![[how_dose_changesets_work.svg]]

> [!tip]
> 由于 Changesets  只专注于发布和变更的版本和日志,对于仓库的更改如果不需要这些就无需添加 changeset。所以,我们建议在缺少 changeset 的情况下,不应当一味的去阻止合并请求。

## 其它特性

### 固定包

>[!tip]
>适用于一组包同时进行变更和发布。

一组包共享同一套版本控制和发布流程，即使其中一些包没有做任何变更。
在 `.changeset/config.json` 文件中通过 `fixed` 字段声明一组固定的包。
```json
{
	"fixed":[["pkg-a","pkg-b"]]
}
```

假设有一些包，其中 `pkg-a` 与 `pkg-b` 是一组“固定包”，`pkg-c`不是。它们当前的版本如下：
- `pkg-a@1.0.0`
- `pkg-b@1.0.0`
- `pkg-c@1.0.0`

现在创建变更集文件，对 `pkg-a` 进行补丁变更，`pkg-b` 进行次版本变更，`pkg-c` 进行主版本变更，然后消耗变更集并发布，结果版本情况如下：
* `pkg-a@1.1.0`
* `pkg-b@1.1.0`
* `pkg-c@2.0.0`

继续创建变更集文件，只对 `pkg-a` 进行小版本变更，然后发布，版本情况如下：
* `pkg-a@1.2.0`
* `pkg-b@1.2.0`
* `pkg-c@2.0.0`

**glob 表达式匹配**

`fixed` 字段支持使用 `glob` 表达式匹配特定模式的包。
```json
"fixed":[["pkg-*"]]
```

### 联动包

>[!tip]
>适用于“核心包”和一组依赖其的“插件包”。

对一组包进行版本控制，与“固定包”不同之处在于：
1. 允许联动包集中的包单独变更（只限拥有变更集的包才能进行变更）。 ^f80c51
2. 允许联动包集中的包单独发布。
3. 当联动包集中的一些包同时变更时，会用其中版本提升最高的哪个包的版本作为其它包最终要提升的版本。 ^659c15

使用 `linked` 字段可以定义一组“联动包”：
```json
{
	"linked":[["core", "plugin-a", "plugin-b"]]
}
```

`plugin-a` 与 `plugin-b` 依赖 `core`，版本情况如下：
* `core@1.0.0`
* `plugin-a@1.0.0`
* `plugin-b@1.0.0`

创建变更集文件， `plugin-a` 补丁变更，现在版本情况如下:
* `core@1.0.0`
* `plugin-a@1.0.1`*（满足 [[#^f80c51]]）*
* `plugin-b@1.0.0`

创建变更集文件 `plugin-a` 补丁变更，`plugin-b` 次版本变更，现在版本情况如下：
* `core@1.0.0`
* `plugin-a@1.1.0`*（满足 [[#^659c15]]）*
* `plugin-b@1.1.0`*（满足 [[#^659c15]]）*

创建变更集，核心包 `core` 进行主版本变更,现在的版本情况如下（依赖者包也满足 [[#^659c15]]）：
* `core@2.0.0`
* `plugin-a@2.0.0`
* `plugin-b@2.0.0`

与 “固定包”相同，联动包也支持 `glob` 匹配模式：
```json
{
	"linked": [["core","plugin-*"]]
}
```
 
### 快照发布

快照版本主要用于验证和测试。非常类似与“预发行版”，但又没有后者复杂的版本提升规则。
创建快照发布，只需在执行 `version` 命令时添加一个 `--snapshot` 参数即可。
```shell
npm run changeset version -- --snapshot
```

Changesets 默认会在当前的版本号的基础上再添加时间戳后缀来生成快照版本。例如当前的版本是 `core@3.0.0`，在进行版本提升后则为：`core@3.0.0-20240830025246`。

快照版本支持添加 `tag`，以增强版本号的个性化。
```shell
npm run changeset version -- --snaphost canary
# `core@3.0.0-canary-20240830025246`
```

最后便是发布快照版本:
```shell
npm run changeset publish -- --tag cannary --no-git-tag
```
`--tag` 参数可以用自定义的“分发标签”发布到 npm 上，而不是覆盖默认的 `latest` 标签，这非常重要。
`--no-git-tag` 跳过为快照包创建 git 标签（只有稳定版本是才有，这是默认行为）。

>[!warning]
>“快照版本”不是“稳定版本”。因此不建议将更改合并到主分支上，你可以单独用一个分支去保存快照的版本和标签，但记住，永远也不要将这个分支合并到主分支上。

### 预发行版

预发行版非常复杂，Changesets 专门用两种变更模式来区分“预发行版”和“稳定版”。

想要发布预发行版，必须要先进入“预发布模式”：
```shell
npm run changeset pre enter alpha
```
* `pre enter` 是进入预发布模式的命令关键字。
* `alpha` 是一个标签，在最后发布时会被用做“分发标签”。

进入“预发布模式”后，Changesets 会在 `.changeset` 目录下创建一个 `pre.json` 的文件，用于记录预发行版本的状态和信息。
```markdown
{
  "mode": "pre",
  "tag": "beta",
  "initialVersions": {
    "core": "6.0.0",
    "pkg": "2.2.0",
    "plugin": "3.8.0"
  },
  "changesets": []
}
```

在预发布模式下我们可以像以往那样使用 `changeset` 命令创建变更集文件；使用`changeset version` 命令来消耗变更集文件来实现版本提升与日志记录。但有一点非常不同，那就是**在预发布模式下消耗变更集文件并不会实际删除 `.changeset` 目录下的变更集文件**。

在“预发布模式”下消耗变更集文件会递增预发布版本计数，即从默认的 `-alpha.0` 递增为 `-alpha.1`

当需要发布预发行版本时，正常执行 `changeset publish` 命令即可，此时 `alpha` 标签将会作为 NPM 包的 dist 标签发布到 Registry 上。

以上大致操作的大致流程可能如下：
```shell
npm run changeset pre enter alpha

npm run changeset # create changeset files
npm run changeset version # alpha.0
npm run changeset # create changeset files
npm run changeset version # alpha.1

git add .
git add *
git commit -m 'feat: 进入预发布模式发布 xxx-alpha.1 版本'
npm run changeset publish
git push --follow-tags

npm run changeset pre exit # exit pre mode
```

若想再次期间变更 tag，只需先退出预发布模式，然后用新的 tag 再进入预发布模式即可：
```shell
npm run changeset pre exit
npm run changeset pre enter beta
```

当预发行的流程走完需要发布最新阶段的稳定版时，你需要退出预发布模式，然后执行 `changeset version` 命令来实际消耗变更集文件，Changeset 也会自动删除 `pre.json` 文件。
```shell
npm run changeset pre eixt
npm run changeset version

git add .
git add *
git commit -m 'feat: 发布最新阶段的正式版本'

npm run changeset publish
git push --follow-tags
```
发布命令将像往常一样将所有内容发布到 `latest` 标签上。
## 配置文件

认识一下 `.changeset/config.json` 配置文件相关的字段：

### commit

是否要在执行`changeset add` 或 `changeset version` 命令时调用 `git add` 与 `git commit` 并自动生成提交消息？
默认情况下，不会自动添加与提交文件，而是让用户提交文件。
取值：
* 布尔值：设置为 `true` 时自动执行 git 添加与提交行为，并使用 Changeset 默认提供的 `cli/src/commit` 模块生成提交消息。
* 字符串类型：自定义生成提交消息的模块路径。该模块需要导出两个函数：`getAddMessage` 与 `getVersionMessage` 用于执行 `changeset add` 或 `changeset version` 命令时生成自定义的 git 提交消息。
* 数组类型：自定义生成提交消息的模块路径并可以自定义选项。
* 默认值为：`false`。

下面是自定义生成提交消息的用例：
```json
{
  "commit":["../.changeset/commit.js", {"skipCI":"add"}]
}
```

选项中的 `skipCI` 参数会用于 git commit message 中 `[skip ci]` 占位符的生成，以控制 CI 系统是否要跳过该提交的构建流程。

	chore(release): publish new version [skip ci]

模块中导出的 `getAddMessage` 与 `getVersionMessage` 函数具体实现可以参考官方示例：[commit](https://github.com/changesets/changesets/blob/main/packages/cli/src/commit/index.ts)
### access

设置 Changesets 向 Registry 上的发包方式。
取值：
- `restricted`: 私有包。
- `public`: 公共包。
- 默认值：`restricted`。主要是防止用户意外操作。

### baseBranch

设置 Changesets 工作的分支：
取值：
* 默认值：`main`。

### ignore

忽略要发布的包。建议使用 `package.json` 文件的 `private` 字段。
取值：
- 包名的字符串数组。

### fixed

指定一组固定包。共享同一套语义版本控制与发包流程。
取值：
- 包名的二维数组。

### linked

指定一组联结包。共享同一套语义版本控制，但是允许单独提升版本与发布。
取值：
- 包名的二维数组。

### updateInternalDependencies

控制依赖者包版本提升时的提升类型（详细描述：[[#^a005e5]]）
取值：
- 默认值：`patch` 

### changelog

设置变更日志的生成方式。Changesets 默认使用 `@changesets/changelog-git` 包来生成变更日志。

除此之外，对于 `GitHub` 的项目，官方还提供了 `@changesets/changelog-github` 的可选包。专门用于生成 GithHub 下的变更日志。

开发者也可以自定义变更日志的生成逻辑，这需要编写一个模块，并导出两个名为 `getReleaseLine` 与 `getDependencyReleaseLine` 方法。

取值：
* 布尔值： `false` 表示不生成变更日志。
* 字符串： 模块的路径。
* 数组：模块的路径与选项。
* 默认值：`"@changesets/cli/changelog"`

示例：
```json
{
	"changelog":["@changesets/changelog-github", {"repo": "xxx"}]
}
```

### bumpVersionsWithWorkspaceProtocolOnly

确定 Changesets 是否仅更新使用工作区协议且属于工作区的包的依赖范围。

### snapshot

- `useCalculatedVersion`
	- 指定快照版本的基础版本，默认 `0.0.0` 。
- `prereleaseTemplate`: 
	- 自定义快照版本的后缀。
	- 可以使用的占位符有：`{tag}`、`{commit}`、`{timestamp}`、`{datetime}`。
	- 默认占位符为：`{tag}-{datetime}` 。

### schema
更多的配置字段和相关说明可以查阅 [schema](https://unpkg.com/@changesets/config@3.0.2/schema.json) 文件：

## 命令与选项

命令行是与 Changesets 交互的主要方式。
下面是 Changesets 提供的全部命令，但主要命令只有四个：
- `init`
- `add`
- `version`
- `publish`

### init

初始化项目，该命令只需要执行一次。
此命令会在项目根目录创建 `.changeset` 隐藏目录，并生成配置文件、自述文件。

### add

创建变更集文件。
```shell
changeset add
#或
changeset 
```

此命令会以交互问卷的方式获取变更的包、要提升的版本类型以及变更日志信息，最后生成变更集文件。

选项参数：
* `--empty`
  创建空的变更集文件。通常只有在您有阻止合并的 CI 时才需要。
* `--open`
  创建变更集文件，并用外部编辑器打开。

### version

消耗变更集文件进行版本提升。
此命令会修改包 `package.json` 的 `version` 字段，同时在包根目录下创建 `CHANGELOG.md` 文件以记录日志信息。

选项参数：
* `--snapshot`
  使用快照发布。

>[!tip]
>我们建议在运行 publish 之前，确保从这个命令生成的更改被合并回主分支。

### publish

创建git标签并发布包。其底层是对包管理器 `publish` 命令的封装。
此命令会检查本地包的版本与 Registry 上的版本，如果未发布，便会执行发布动作。

>[!warning]
>由于此命令假定最后一次提交为发布提交，所以在调用 version 和 publish 之间不应有任何其他提交变更的操作。这两个命令分开执行是为了让你能检查将要发布的更改是否准确无误。

选项参数：
- `--otp={passowrd}`
  如果包管理器的用户开启了双因素认证，则需要该选项指定一次性密码。
- `--tag {TAGNAME}`
  可用于覆盖默认的 `latest` 标签，将版本发布到自定义分发标签。

Git 标签：
如果 `config.json` 中的 `commit:false` 则 `publish` 命令创建的 git 标签便不会自动提交到远程仓库，因此需要手动推送：
```shell
git push --follow-tags
```

### status

返回 Changesets 当前的状态信息。

没有变更时：
```shell
➜  changesets-test git:(master) npm run changeset status
🦋  info NO packages to be bumped at patch
🦋  ---
🦋  info NO packages to be bumped at minor
🦋  ---
🦋  info NO packages to be bumped at major
```

对 `core` 进行次版本变更，`pkg` 进行修补后：
```shell
➜  changesets-test git:(master) ✗ npm run changeset status
🦋  info Packages to be bumped at patch:
🦋  info 
🦋  - pkg
🦋  ---
🦋  info Packages to be bumped at minor:
🦋  info 
🦋  - core
🦋  ---
🦋  info NO packages to be bumped at major
```

选项参数：
- `--verbose`
  返回“变更集”文件的链接与预期要提升的新版本。
- `--output={"FILE_NAME"}`
  将状态信息以 JSON 格式输出到指定的文件中，以供其它工具使用。
- `--since`
  仅显示自特定分支或 git 标签以及 Commit 号(如 `main` 或最新版本的 git 哈希值)之后的 changesets 信息。（这可以帮助我们检查是否需要“变更集”文件，但是更推荐使用 `changeset bot` 来检查。）


### pre

进入/退出“预发布模式”。
此命令必须指定预发布的标签。
此命令会在 `.chagneset` 目录下创建 `pre.json` 文件。

### tag

使用包的版本来创建git标签。
该命令的行为与 `changeset publish` 创建git标签相同。主要用于自定义发包的场景，比如用 `npm run publish -ws` 替代 `changeset publish`，此时 `changeset add` 可以很方便的为所有包创建 git 标签。 

通常是在 `changeset tag` 之前运行 `chagneset version` 命令，以便在创建 git 标签之前更新 `package.json` 版本。

最后记得手动推送下：
```shell
git push --follow-tags
```

>[!tip]
>在 Monorepo 中会用包名作为 tag 的前缀 —— `core@5.0.0`
>在 Single Package Repo 中会使用 `v` 作为 tag 前缀 —— `v1.0.0`

## 修改日志格式

Changesets 默认使用内置的 `@changesets/changelog-git` 包生成默认的日志信息。若要自定义日志生成格式并添加额外信息，可以通过配置文件的 `chagnelog` 字段来自定义日志生成方法。

通过查看 `@changesets/changelog-git` 源码可知它主要实现了以下两个方法：
- `getReleaseLine`：获取当前的版本发布线。
- `getDependencyReleaseLine`：获取依赖的版本发布线。

> 从上一次 `chagneset publish` 后到最近一次将要发布的所有变更就是一条版本发布线（Release Line）。

下面是方法的执行时机：
* `getReleaseLine` 方法会在消耗变更集文件时为每个受影响的包调用一次，如果一个 changeset 前言数据列出了多个受影响的包，这会为每个包都调用一次该方法，以生成日志信息。
* `getDependencyReleaseLine` 该方法会在“包”的依赖包发生变更时调用，用于生成依赖者包的日志信息。因为可能会有多个依赖，所以这里接收的参数是一个数组。

下面是方法参数说明：
- `getReleaseLine(changeset, type, options)`
 ```typescript
type VersionType = 'patch' | 'minor' | 'major';
type Changeset = {
	summary:string; //当前 changeset 文件的摘要，如果有行，则用\n表示换行。
	id:string // changeset 文件的名称。
	// 返回最近一次包含变更集合文件的 commit 号。 如果开启了自动 git 添加与提交。
	commit:string 
	// 一个数组，如果一个 changeset 文件中有多个包。
	release:{name:string, type:VersionType}[] 
}

type Type = VersionType; //要提升的版本类型。
```

- `getDependencyReleaseLine(changesets, dependenciesUpdated, options)`
```ts
type Changesets = Changeset[];
type dependenciesUpdated = {
	name:string //依赖包的包名
	type:VersionType //版本提升类型。
	oldVersion:string // 旧版本
	newVersion:string // 提升的新版本。
	changesets:string[] //变更集文件名数组
	packageJson:Record<string,string> //依赖包的 package.json 文件内容
	dir:string //依赖包的位置。
 }
```

第三个参数 `options`，可以配置自定义模块时一同传入：
```json
{
	"changelog":["./changelog.js", {"repo": "xxx"}]
}
```
具体实现可以参考 `@changesets/changelog-github` 包。

下面是自定义模块的具体实现：
```ts
import { NewChangesetWithCommit, VersionType, ChangelogFunctions, ModCompWithPackage } from '@changesets/types';

function getCommit(commit?: string) {
  if (!commit) {
    return '';
  }
  return `[${commit}](https://git.gametaptap.com/web-frontend/web-internal/tds-iem-mono/commits/${commit})`;
}

const getReleaseLine = async (changeset: NewChangesetWithCommit, _type: VersionType) => {
  const [firstLine, ...futureLines] = changeset.summary.split('\n').map((l) => l.trimRight());

  let returnVal = `
#### ${firstLine}(${getCommit(changeset.commit)})`;

  if (futureLines.length > 0) {
    returnVal += `\n${futureLines.map((l) => `${l}`).join('\n')}`;
  }

  return returnVal;
};

const getDependencyReleaseLine = async (
  changesets: NewChangesetWithCommit[],
  dependenciesUpdated: ModCompWithPackage[],
) => {
  if (dependenciesUpdated.length === 0) return '';

  const changesetLinks =
    '如下 commit(' + changesets.map((changeset) => ` ${getCommit(changeset.commit)} `).join(',') + ') 更新了依赖：';

  const updatedDepenenciesList = dependenciesUpdated.map(
    (dependency) => `  - ${dependency.name}@${dependency.newVersion}`,
  );

  return ['\n#### 依赖变更',changesetLinks, ...updatedDepenenciesList].join('\n');
};

const defaultChangelogFunctions: ChangelogFunctions = {
  getReleaseLine,
  getDependencyReleaseLine,
};

export default defaultChangelogFunctions;

```

>[!warning]
>在执行 `changeset version` 时，如果要消耗的变更集文件没有提交到 git 中，那么 `getReleaseLine` 与 `getDependencyReleaseLine` 两个方法的 `changeset` 参数的 `commit` 属性值可能为 `undefined`。

## 自动化流程



## 常见问题

### 什么时候需要添加多个变更集文件？

- 您想要发布具有不同更新日志条目的多个软件包 —— 更新了多个包。
- 您对包进行了多项更改，每项更改均应单独调用 —— 做了多次不同的更改。

### 如何编写变更集文件？

1. WHAT the change is  （变更的内容是什么？）
2. WHY the change was made  （变更的原因）
3. HOW a consumer should update their code （消费者如何更新他们的代码）

---
## 参考

- [Changesets: 流行的 monorepo 场景发包工具](https://zhuanlan.zhihu.com/p/427588430)
- [Changesets 官方中文文档](https://changesets-docs.vercel.app/zh-CN)