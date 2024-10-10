## 重置到指定 commit

使用 `git log` 命令查看历史提交情况，找到要重置到的 commit 号。

```shell
git log
git reset --hard 13327c4 #HEAD is now at 13327c4 commit message 
```

重置完成之后，`13327c4` 之后的**所有提交及其它们的更改都会丢失**。如果想将丢失的更改保存到暂存区（stage），可以使用 `--soft` 参数

```shell
git reset --soft 13327c4
```

## 恢复指定的 commit

使用 `git reflog` 命令查看“游标(HEAD)”的移动情况，找到需要恢复的 commit 号。

```shell
git reflog
git reset --hard 71ead22
```

恢复完成后，`71ead22` 之后的**所有提交也会丢失，包括它们的更改**，如果需要将这些更改保存到暂存区，可以使用 `--soft` 参数。

```shell
git reset --soft 71ead22
```

>[!NOTE]
>重置与恢复commit，本质都是移动“游标”指向，对于恢复而言，只是移动到一个不存在于当前开发历史提交的 commit。

## 取消最近一次提交

```shell
git reset --hard HEAD^1
```

- `HEAD^1`：表示将“游标”移动到当前分支当前提交的上一个提交。

如果需要保存更改到暂存区，可以使用 `--soft` 参数。

```shell
git reset --soft HEAD^1
```

## 将更改追加到最近一次提交

```shell
git add *
git commit --amend
```

## 向历史提交追加更改

暂存当前的更改：
```shell
git stash save 'temp-0'
```

查看提交历史，获取要追加的历史提交的父 commit 号。
```shell
git log 
```

假如要追加的历史 commit 号为：`9b27a2e0d8`
历史提交的父 commit 号为：`a201bb0d8f5`

现在，使用父 commit 号来执行 `git rebase` 命令，通过 `-i` 打开交互式命令行。
```shell
git rebase -i a201bb0d8f5
```

在编辑器中找到 commit 号为 `9b27a2e0d8`的条目（即要追加的目标 commit），将其前面的 `pick` 修改为 `edit` 然后保存退出。

此时处于 `rebase` 模式下，HEAD 当前指向的 commit 便是 `9b27a2e0d8`。

恢复暂存区：
```shell
git stash pop
```

添加变更，并追加到当前 commit 中：
```shell
git add *
git commit --amend
```

保存，然后执行 `rebase --continue` 退出 rebase 模式：
```shell
git rebase --continue
```

## 修改最近一次提交信息

```shell
git commit --amend
```

## 修改历史提交的提交信息

使用 `rebase` 来编辑历史提交的提交信息。

通过 `git log` 查看历史提交情况，然后找到目标提交的上一个提交的 commit 号。也可以通过 `git rev-parse <commit-hash>^1` 快速获取当前 commit 号的上一个 commit 号。
 
```shell
git rebase -i $(git rev-parse f5da2c24^1)
```

将需要修改的 commit 由 `pick` 改变为 edit ，然后 `wq` 保存。

```text
edit f5da2c24 chore(global): codeowner 增加 ssp-landing 项目
pick f565ebf3 chore(ssp-landing): 调整 ci 配置
pick 6bae6c47 feat(ssp): 回复 ssp api 为上一个版本

# Rebase 94de52e7..6bae6c47 onto 94de52e7 (3 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
```

这一步的目的便是将“游标(HEAD)” 指向到 `f5da2c24`，使用 `git log` 可以发现要编辑的那个 commit 已经变为了最近一次提交，所以再通过 `git commit --amend` 的方式修改即可。最后在结束 rebase 操作。

```shell
git commit --amend 
git rebase --continue
```