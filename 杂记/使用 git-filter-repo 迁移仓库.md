## 简介

使用 `git-filter-repo` 工具可以将一个仓库的代码迁移到另一个仓库的指定位置下，同时还不丢失源仓库的提交记录和 tag。这对于需要将多个独立仓库迁移合并到 MonoRepo 仓库中非常有用。

## 安装

* `git >= 2.22.0`
* `python3 >= 3.6`

**macos**

```shell
brew install git-filter-repo
```

**windows**

```shell
scoop install git-filter-repo
```

**linux**

```shell
apt/yum install git-filter-repo
```

## 准备工作

```shell
mkdir app
cd app
git clone git@git.gametaptap.com:xxxx/source_repo.git #源仓库
git clone git@git.gametaptap.com:xxxx/target_repo.git #目标仓库

cp -r ./target_repo ~/backup/ # 备份源仓库，便于下次在拷贝回来。

➜  app tree -L 1
├── source_repo
└── target_repo
```

>[!WARNING]
>请注意备份源仓库，因为 `git-filter-repo` 工具的操作是破坏性的，一旦中途结果不符合我们的要求，可能需要重新 clone 一份源仓库。

## 处理源仓库

### 重命名 tag

处理源仓库的 tag，用源仓库名称作为前缀，在合并后用于标识区分：
```shell
cd source_repo
git checkout master   
git pull origin master -v --ff

git filter-repo --tag-rename ":source_reop_name-"
git filter-repo --tag-rename "v:source_repo_name-v-" #与上面等价
```

- `master` 默认分支，可以按需求自定义
- `-v` 即 `verbose` 输出详细日志
- `--ff` 即 `--ff-only` 指定合并策略为快进合并（fast-forward）。
- 替换 `source_repo_name` 为你的真实仓库名称。

### 重写 commit 消息

为源仓库的 commit 信息添加前缀，用于合并后的标识区分：
```shell
cd source_repo
git checkout master
git pull origin master -v --ff

git --message-callback "return b'''[source_repo_name] ''' + message"
```

- `b'''[source_repo_name] '''` 是 `Python` 中的语法，用于表示一个字符串。

### 移动仓库代码到指定位置

```shell
cd source_repo
git checkout master 
git pull origin master -v --ff

git filter-repo --to-subdirectory-filter "assets"
```

- `assets` 目录名称。

### 提取指定目录的代码和提交记录

```shell
cd source_repo
git checkout master 
git pull origin master -v --ff

git filter-repo --to-subdirectory-filter "app" --path "packages/package-1"
```


### 重命名提取的目录路径

```shell
git filter-repo --path "packages/package-1" --path-rename "packages/package-1:libs"
```


## 合并到目标仓库

```shell
cd ../target_repo
git checkout merge_source_repo

# 将源仓库作为目标仓库的远程仓库
git remote add source_repo ../source_repo -f
git merge --allow-unrelated-histories -m "Merge remote-tracking branch source_repo/master" source_repo/master

git remote remove source_repo
```

- `-f` 即 `--fetch`, 添加远程仓库时立即执行 `git fetch`
- `--allow-unrelated-histories` : 用来合并没有共同提交历史的两个分支（常出现于两个独立初始化的仓库合并场景）

## shell 脚本

```shell
#!/bin/bash
set -euo pipefail
# set -x

# get source repo from user input
read -p "Enter source repo name: " source
if [ -z "$source" ]; then
  echo "source repo required"
  exit 1
fi
# get subdirectory from user input
read -p "Enter subdirectory: " subdirectory
if [ -z "$subdirectory" ]; then
  echo "subdirectory required"
  exit 1
fi
# get target repo from user input
read -p "Enter target repo name: " target
if [ -z "$target" ]; then
  echo "target repo required"
  exit 1
fi
# get branch from user input
read -p "Enter branch: " branch
if [ -z "$branch" ]; then
  branch="master"
fi

# pull source repo
(
    cd $source
    git checkout $branch
    git pull origin $branch -v --ff
    filter="$source"
    if [ "$subdirectory" != "" ];then
      filter="$subdirectory/$source"
    fi
    git filter-repo --force --to-subdirectory-filter "$filter" --tag-rename ":$source-" --message-callback "return b'''[$source] ''' + message"
)

# merge
(
    cd $target
    git remote add -f $source ../$source
    git merge --allow-unrelated-histories -m "Merge remote-tracking branch $source/$branch" $source/$branch
    git remote remove $source
)
```

## 更多用例

`git-filter-repo` 还可以：
- 去除大文件（或大目录或大扩展名）
- 按路径去除不需要的文件
- 替换或删除敏感文本，如密码

更多用例：
https://www.mankier.com/1/git-filter-repo#Examples

自定义 callback 函数：
https://www.mankier.com/1/git-filter-repo#Callbacks
