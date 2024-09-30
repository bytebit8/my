## npm init

查阅：[[20 × NPM CLI - init & create]]

## npm install

查阅：[[21 × NPM CLI - install]]

## npm uninstall

卸载指定的“包”。

- 删除 `node_modules` 目录下的包。
- 更新 `package.json` 文件。
- 更新 `package-lock.json` 文件。

### 常用别名

```shell
npm rm
npm remove
```

### 工作模式

本地模式：
```shell
npm uninstall lodash
```

全局模式：
```shell
npm uninstall chalk-cli -g 
```


### 工作区支持

- `-ws` 参数可以在工作区的所有子包中移除指定的依赖（但不会移除根包中的依赖）
- `-w` 参数可以过滤子包，卸载指定子包中的依赖。

```bash
npm rm date-toolkit           # 卸载当前包的依赖。
npm rm lodash -ws             # 卸载所有子包中的依赖。
npm rm glup -w packages/app1  # 从指定子包中卸载依赖。
```

### 常用参数
| 参数          | 说明                                                |
| ----------- | ------------------------------------------------- |
| `-g`        | `--global`                                        |
| `--no-save` | 只删除包不更新 `packages.json` 与 `package-lock.json` 文件。 |
| `-w`        | `--workspace`                                     |
## npm ci

查阅：[[22 × NPM CLI - ci]]

## npm update

更新依赖（升级依赖版本）。
`npm update` 命令通常还会与 `npm outdated` 命令结合使用。

### 常用别名

```shell
npm up
```


### 依赖更新的规则

- 只会将依赖的版本更新到满足语义化版本范围内的最新版本。
- 不会更新 `package.json` 文件，但会更新 `package-lock.json` 文件。

```shell
npm update
npm update -g
npm update lodash
```

### 强制更新 package.json

使用 `--save` 参数可以强制更新 `package.json` 中依赖信息有关的版本范围。
```shell
npm up --save
```

### 工作模式

全局模式：
```shell
npm up -g
```


本地模式：
```shell
npm up 
npm up lodash
```


### 工作区支持

- 默认更新整个工作区的依赖。
- 若位于某个子包，则会只更新当前子包的依赖。
- 使用 `-ws` 参数可以更新工作区所有子包的依赖（但不会更新根包的依赖）。
- 使用 `-w` 参数可以过滤子包，更新指定子包的依赖。

```bash
npm up                       # 更新整个工作区的依赖。
npm up -ws                   # 更新所有工作区中的依赖 或 当前包的依赖。
npm update -w packages/app1  # 更新指定工作区中的依赖。
```

### 常用常用参数
| 参数               | 说明                                                 |
| ---------------- | -------------------------------------------------- |
| `—save`          | 升级依赖，并更新 `package.json` 以及 `package-lock.json` 文件。 |
| `-g`             | `—global`                                          |
| `—save-dev`      | 只更新 `devDependencies` 字段中声明的依赖。                    |
| `—save-prod`     | 只更新 `dependecies` 字段中声明的依赖。                        |
| `—save-optional` | 只更新 `optionalDependencies` 字段中声明的依赖。               |
| `—save-peer`     | 只更新 `peerDependencies` 字段中声明的依赖。                   |
| `—save-bundle`   | 只更新 `bundleDependencies` 字段中声明的依赖。                 |
| `—omit`          | 忽略更新指定依赖字段声明的依赖。 `dev`                             |
| `-w`             | 更新指定工作区的依赖。                                        |
| `--loglevel`     | 日志等级。`verbose`                                     |
| `--timing`       | 计时信息。                                              |
## npm ls

查阅：[[24 × NPM CLI - ls]]

## npm search

使用搜索词在注册表中搜索包。

### 常用别名

```shell
npm find
npm s
```

### 限制条数搜索

```shell
npm s express --searchlimit 100
```

### 自定义注册表搜索

```shell
npm search vue --registry https://registry.npmjs.org
```

### 常用参数
| 参数              | 说明                       |
| --------------- | ------------------------ |
| `--json`        | 将搜索结构输出为 JSON 的格式。       |
| `--registry`    | 自定义要搜索的注册表地址，默认 npm 注册表。 |
| `--searchlimit` | 设置搜索的条数。                 |
## npm run

查阅：[[23 × NPM CLI - run]]

## npm test

内置脚本，测试一个包。运行包内置的 `test` 脚本命令。
```shell
npm test # 等价 npm run test
```

### 环境变量

1. 会将项目的 `./node_modules/.bin` 路径加入到系统环境变量 `PATH` 中。
2. 会解析 `package.json` 的字段，以 `npm_package_*` 为前缀格式加入到环境变量中。

### 工作区支持

如果某些子包的 `package.json#scripts` 字段没有定义 `test` 脚本，可以附加 `--if-present` 来只执行存在 `test` 脚本的子包。

### 常用参数
| 参数                         | 说明                              |
| -------------------------- | ------------------------------- |
| `--wrokspaces`             | `-ws`                           |
| `--workspace`              | `-w`                            |
| `--include-workspace-root` | 执行的动作包含根包。                      |
| `--if-present`             | 静默执行命令。                         |
| `--ignore-scripts`         | 忽略命令的 `pre` 与 `post` 等命令钩子。     |
| `--foreground-scripts`     | 将一些默认在后台运行的生命周期脚本，放在<br>前台运行输出。 |
| `--script-shell`           | 用于自定义命令行程序，例如 `bash` 等。         |
| `--loglevel`               | 日志等级。`verbose`                  |
| `--timing`                 | 计时信息。                           |
## npm start

内置脚本，启动包。运行包内置的 `start` 命令。
```shell
npm start
```

### 环境变量

1. 会将项目的 `./node_modules/.bin` 路径加入到系统环境变量 `PATH` 中。
2. 会解析 `package.json` 的字段，以 `npm_package_*` 为前缀格式加入到环境变量中。

### 缺省默认

如果包的 `package.json#scripts` 字段没有定义 `start` 命令，npm CLI 则默认执行 `node server.js`。

给定项目结构：
```text
pkg
├── package.json
└── server.js
```

`server.js` 文件内容：
```javascript
#!user/bin/env node
console.log("Hello Node Server!");
```

缺省 `package.json#scripts.start` 配置，此时执行 `npm start`，观察命令输出：
```shell
➜  pkg npm start
# > node server.js
# Hello Server.js
```

### 工作区支持

搭配 `--if-present` 命令选项一同使用。

### 常用参数
| 参数                         | 说明                              |
| -------------------------- | ------------------------------- |
| `--wrokspaces`             | `-ws`                           |
| `--workspace`              | `-w`                            |
| `--include-workspace-root` | 执行的动作包含根包。                      |
| `--if-present`             | 静默执行命令。                         |
| `--ignore-scripts`         | 忽略命令的 `pre` 与 `post` 等命令钩子。     |
| `--foreground-scripts`     | 将一些默认在后台运行的生命周期脚本，放在<br>前台运行输出。 |
| `--script-shell`           | 用于自定义命令行程序，例如 `bash` 等。         |
| `--loglevel`               | 日志等级。`verbose`                  |
| `--timing`                 | 计时信息。                           |
## npm restart

内置脚本，重启一个包。运行包内置的 `restart` 命令。
```shell
npm restart
```

该命令默认会将项目的 `./node_modules/.bin` 路径加入到系统环境变量 `PATH` 中。

### 缺省默认

如果包的 `package.json` 中没有定义 `restart` 命令，则执行 `npm restart` 相当于执行如下的命令组合：
```shell
npm stop --if-present;
npm start
```

### 环境变量

1. 会将项目的 `./node_modules/.bin` 路径加入到系统环境变量 `PATH` 中。
2. 会解析 `package.json` 的字段，以 `npm_package_*` 为前缀格式加入到环境变量中。
### 工作区支持

建议配合 `--if-present` 命令选项一同使用。

### 常用参数
| 参数                         | 说明                              |
| -------------------------- | ------------------------------- |
| `--wrokspaces`             | `-ws`                           |
| `--workspace`              | `-w`                            |
| `--include-workspace-root` | 执行的动作包含根包。                      |
| `--if-present`             | 静默执行命令。                         |
| `--ignore-scripts`         | 忽略命令的 `pre` 与 `post` 等命令钩子。     |
| `--foreground-scripts`     | 将一些默认在后台运行的生命周期脚本，放在<br>前台运行输出。 |
| `--script-shell`           | 用于自定义命令行程序，例如 `bash` 等。         |
| `--loglevel`               | 日志等级。`verbose`                  |
| `--timing`                 | 计时信息。                           |
## npm stop

内置脚本，停止一个包。执行包内的 `stop` 命令。

### 环境变量

1. 会将项目的 `./node_modules/.bin` 路径加入到系统环境变量 `PATH` 中。
2. 会解析 `package.json` 的字段，以 `npm_package_*` 为前缀格式加入到环境变量中。
### 工作区支持

建议配合 `--if-present` 命令选项一同使用。

### 常用参数
| 参数                         | 说明                              |
| -------------------------- | ------------------------------- |
| `--wrokspaces`             | `-ws`                           |
| `--workspace`              | `-w`                            |
| `--include-workspace-root` | 执行的动作包含根包。                      |
| `--if-present`             | 静默执行命令。                         |
| `--ignore-scripts`         | 忽略命令的 `pre` 与 `post` 等命令钩子。     |
| `--foreground-scripts`     | 将一些默认在后台运行的生命周期脚本，放在<br>前台运行输出。 |
| `--script-shell`           | 用于自定义命令行程序，例如 `bash` 等。         |
| `--loglevel`               | 日志等级。`verbose`                  |
| `--timing`                 | 计时信息。                           |
## npm outdated

列出项目中过时的依赖。配合 `update` 命令可以更有效的更新依赖。

### 检查直接依赖

```shell
npm outdated
```

### 色彩区分

在支持色彩输出的命令终端中，如果包名为红色，表明存在兼容的更新，如果为红色，表示存在不兼容的更新。


### 获取更多信息

```shell
npm outdated -l
```

### 检查所有依赖

直接依赖+传递依赖：
```shell
npm outdated -a
```

### 导出 json 文件

更好的便于查看，程序化处理：
```shell
npm outdated -l -a --json
```

### 工作模式

检查全局依赖
```shell
npm outdated -g
```

### 工作区支持

- **默认会检查整个工作区的过时依赖（根包+所有子包）。**
- 若位于某个子包，则只检查当前子包的过时依赖。
- `-w` 参数可以过滤子包，检查指定子包的过时依赖。
- `-ws` 参数可以检查工作区中所有子包的过时依赖（不含根包）。

### 常用参数
| 参数             | 说明                     |
| -------------- | ---------------------- |
| `--global`     | `-g`                   |
| `--all`        | `-a`                   |
| `-l`           | 输出更多信息。包含过时依赖的依赖类型与首页。 |
| `--workspaces` | `-ws`                  |
| `--workspace`  | `-w`                   |
| `--json`       | 以 JSON 格式输出检查的过时依赖。    |
## npm version

内置脚本，修改包的版本号。遵循语义化版本控制。

### 标准版本号修改

```shell
npm version major
npm version minor
npm version patch
```

### 固定版本号

```shell
npm version 1.1.1
```

### 先行版本号修改

```shell
npm version premajoar
npm version preminor
npm version prepatch
```

### 先行版本号 ID

自定义先行版本号标识符的 ID
```shell
npm version premajor --preid alpha
```

### 先行版本号计数

```shell
npm version prerelease
```

### 阻止默认的 Git 行为

在具有版本控制的项目中，执行 `npm version` 命令变更版本号会自动创建 `tag` 并提交 commit。如果要阻止该行为，可以使用 `--no-git-tag-version` 命令选项。
```shell
npm version major --no-git-tag-version
```

### 阻止 Git Commit Hookss

使用 `--no-commit-hooks` 可以组织提交 commit 的时候触发 git Hooks。
```shell
npm run minor --no-commit-hooks
```

### 自定义提交信息

使用 `-m` 参数可以自定义 commit 的信息。
```shell
npm version patch -m "[%s] Released."
```

### 环境变量

1. 会将项目的 `./node_modules/.bin` 路径加入到系统环境变量 `PATH` 中。
2. 会解析 `package.json` 的字段，以 `npm_package_*` 为前缀格式加入到环境变量中。
### 工作区支持

- 默认只语义化变更当前包的版本号（根包/子包）。
- `-w` 参数可以变更指定子包的版本号。
- `-ws` 参数可以变更工作区中所有子包的版本号。（不含根包）

### 常用参数
| 参数                     | 说明                      |
| ---------------------- | ----------------------- |
| `-m`                   | 自定义 git commit message. |
| `--preid`              | 自定义预发布标识符 ID。           |
| `--no-git-tag-version` | 阻止自动打 tag 与 提交 commit   |
| `--no-commit-hooks`    | 禁用 git commit hooks 钩子  |
| `-w`                   | 变更指定工作区的版本。             |
| `-ws`                  | 变更所有工作区的版本。             |
## npm publish

查阅：[[26 × NPM CLI - publish]]

## npm unpublish

### 删除包或版本

删除注册表中指定的包或包的版本。
```shell
npm unpublish foo20xxx
npm unpublish foo20xxx@1.1.1
```

>[!TIP]
>建议废弃 deprecate 包或包的版本，而不是直接删除。

### 惰性废弃

使用 `--dry-run` 命令选项来惰性废弃，并不会实际废弃包或版本，主要用于调试：
```shell
npm unpublish foo20xxx --dry-run
```

### 常用参数
| 参数          | 说明           |
| ----------- | ------------ |
| `--dry-run` | 惰性废弃，主要用于调试。 |
| `-w`        | 废弃指定的子包。     |
| `-ws`       | 废弃工作区所有子包。   |

## npm deprecate

### 废弃包或版本

废弃包或废弃包的指定版本。
```shell
npm deprecate foo20xxx "deprecate this a package"
npm deprecate foo20xxx@2.0.0 "deprecate 2.0.0 version"
```

### 废弃多个版本

通过版范围语法：
```shell
npm deprecate foo20xxx@"<2.0.0 >0.1.0" "deprecate message."
```

### 取消废弃

删除废弃的 message 即可：
```shell
npm deprecate foo20xxx@2.0.0 ""
```

### 常用参数

| 参数           | 说明      |
| ------------ | ------- |
| `--registry` | 自定义注册表。 |

## npm dist-tag

管理包的分发标签。分发标签数据存储在注册表服务中。
分发标签时 NPM 独有的功能，但默认只有一个 `latest` 的分发标签。它永远指向最新发布的版本号。

### 用户认证

因为分发标签是存储在注册表中的，所以管理分发标签之前要登录注册表服务。
```shell
npm login --auth-type=web
```

完成登录后，npm CLI 会将用户认证 Token 记录到用户目录下的 `.npmrc` 文件中。

### 列出包的分发标签

```shell
npm dist-tag ls vue
```

### 添加分发标签

分发标签与包的版本号一一对应。
```shell
npm dist-tag add foo20xxx@2.0.0-beta.1 beta
npm dist-tag add foo20xxx@2.0.0-rc.0 rc
```

### 删除分发标签

```shell
npm dist-tag rm beta
```

### 工作区支持

- 默认情况下，只管理当前位置包的分发标签（子包或根包）。
- `-w` 参数可以管理指定包的分发标签。
- `-ws` 参数可以管理工作区中所有子包的分发标签（不含根包）

### 常用参数
| 参数                         | 说明         |
| -------------------------- | ---------- |
| `-w`                       | 废弃指定的子包。   |
| `-ws`                      | 废弃工作区所有子包。 |
| `--include-workspace-root` | 包含根包。      |
## npm login

登录注册表服务。

### 自动登录

身份认证通过后，会将 token 写入到用户目录的 `.npmrc` 文件中。后续便会一直保持登录状态（令牌有效期）
```shell
➜  ~ cat ~/.npmrc
editor=vim
audit-level=high
//registry.npmjs.org/:_authToken=npm_9mTr592JbtmBvbWWPuk*******
```

### 登录方式

通过 `--auth-type` 命令选项可以指定登录的方式。

**通过 Web 登录**
```shell
npm login 
npm login --auth-type=web
```

**使用命令行登录**
```shell
npm login --auth-type=leagcy
```

### 登录到作用域

```shell
npm login --scope=@my-scope --registy=https://registry.npmjs.org
```

### 常用参数
| 参数            | 说明     |
| ------------- | ------ |
| `--registry`  | 自定义注册表 |
| `--auth-type` | 登录方式。  |
| `--scope`     | 作用域    |
## npm adduser

向注册表中添加用户

### 注册方式

**通过 Web 的方式 (默认)**
```shell
npm adduser
npm adduser --auth-type
```

**使用命令行添加**
```shell
npm adduser --auth-type=legacy
```

### 注册作用域账户

```shell
npm adduser --scope=@my-scope --auth-type=web
```

### 常用参数
| 参数            | 说明     |
| ------------- | ------ |
| `--registry`  | 自定义注册表 |
| `--auth-type` | 登录方式。  |
| `--scope`     | 作用域    |
## npm logout

从注册表中退出用户。
使用该命令会导致用户目录下 `.npmrc` 文件中存储的用户令牌(token) 失效。
```shell
npm logout
```

### 常用参数

| 参数           | 说明     |
| ------------ | ------ |
| `--registry` | 自定义注册表 |
| `--scope`    | 作用域    |
## npm whoami

返回当前登录的用户名称。
```shell
npm whoami
npm who
```

### 常用参数
| 参数           | 说明     |
| ------------ | ------ |
| `--registry` | 自定义注册表 |

## npm profile

>[!WARNING]
>请注意，此命令取决于注册表实现，因此第三方注册表可能不支持此接口

读取/更改注册表上的用户信息。

```shell
npm profile get name
npm profile get email

npm profile set name xxx
```

### 开启/关闭账户2FA

```shell
npm profile enable-2fa [auth-only|auth-and-writes]
npm profile disable-2fa
```
- `auth-only` : 只有在登录用户时需要 2FA。
- `auth-and-writes` ：始终都需要 2FA。

### 常用参数
| 参数           | 说明     |
| ------------ | ------ |
| `--registry` | 自定义注册表 |
| `--opt`      | 一次性密码  |
## npm token

用户的访问令牌管理
### 查看访问令牌

```shell
➜  app-mono npm token list
#┌────────┬─────────┬────────────┬──────────┬────────────────┐
#│ id     │ token   │ created    │ readonly │ CIDR whitelist │
#├────────┼─────────┼────────────┼──────────┼────────────────┤
#│ 3dab47 │ npm_Bj… │ 2024-04-11 │ no       │                │
#├────────┼─────────┼────────────┼──────────┼────────────────┤
#│ b91f80 │ npm_YO… │ 2024-04-11 │ yes      │                │
#└────────┴─────────┴────────────┴──────────┴────────────────┘
```

以 json 的格式查看
```shell
npm token list --json
```

```json
[
  {
    "cidr_whitelist": null,
    "readonly": false,
    "automation": false,
    "created": "2024-04-11T11:15:05.178Z",
    "updated": "2024-04-11T11:15:05.178Z",
    "key": "3dab478...",
    "token": "npm_Bj"
  },
  {
    "cidr_whitelist": null,
    "readonly": true,
    "automation": false,
    "created": "2024-04-11T11:14:15.062Z",
    "updated": "2024-04-11T11:14:15.062Z",
    "key": "b91f80...",
    "token": "npm_YO"
  }
]
```

> **注意 token 的 `id` 在 JSON 格式下叫做 `key`**


### 创建访问令牌

```bash
npm token create             # 创建具有写入的访问令牌
npm token create --read-only # 创建只读访问令牌
npm token create --read-only --cidr=192.168.0.1/24 # 创建具有 IP 白名单的访问令牌
```

### 删除访问令牌

```bash
npm token revoke 3dab47;   # 使用 id 删除。
npm token revoke npm_Bj... # 通过 token 删除。
```

### 常用参数
| 参数            | 说明                     |
| ------------- | ---------------------- |
| `--registry`  | 在指定注册表上创建 Token。       |
| `--read-only` | 创建只读的 Token。           |
| `--cidr`      | 创建基于网段 ip 地址白名单的 Token |
| `--json`      | 以 JSON 格式查看 Token 列表。  |
| `--otp`       | 输入一次密码。                |
## npm view

查阅：[[33 × NPM CLI - view]]

## npm pkg

查阅：[[25 × NPM CLI - pkg]]

## npm completion

npm 命令自动补全 （按住 Tab 键）。

输出用户 npm 命令自动补全的脚本：
```bash
npm completion
```

现在将脚本注入到对应终端的配置文件中:
```bash
npm completion >> ~/.zshrc  # zshrc
npm completion >> ~/.bashrc # bashrc

source  ~/.zshrc;
source  ~/.bashrc
```

现在，在终端输入 `npm ins` ，然后按下 tab 键就会出现 `install` 了。

## npm explain

查阅：[[34 × NPM CLI - explain]]

## npm install-test

安装依赖并执行测试脚本。
该命令常用于开发环境的自动化测试流程中。
### 常用别名

```shell
npm it
```

### 安装依赖并执行测试脚本

```shell
npm it glob --save-dev
```

相当于运行下面命令：
```shell
npm i glob;
npm test;
```

### 工作区支持

- 默认只针对当前包有效（根包/子包）
- `-w` 作用于指定的子包。
- `-ws` 作用域工作区所有子包（不含根包）

### 常用参数

参考：[[#npm install]]

## npm install-ci-test

以干净可预测的方式安装项目所有依赖并执行测试脚本。
该命令常用于线上的自动化测试流程。

### 常用别名

```shell
npm cit
```

### 安装依赖并执行测试脚本

```shell
npm cit
```

相当于运行下面命令：
```shell
npm ci;
npm test;
```

### 工作区支持

- 默认只针对当前包有效（根包/子包）
- `-w` 作用于指定的子包。
- `-ws` 作用域工作区所有子包（不含根包）

### 常用参数

参考：[[#npm ci]]

## npm pack

查阅：[[27 × NPM CLI - pack]]

## npm prefix

### 返回当前包的根路径

通常是 `package.json` 与 `package-lock.json` 所在的目录。
```shell
➜  pkg git:(master) ✗ npm prefix
/Users/xxxx/Desktop/Study/pkg
```

### 返回 Node 的 prefix

使用 `-g` 参数：
```shell
➜  pkg git:(master) ✗ npm prefix -g
/Users/xxxx/.nvm/versions/node/v18.18.2
```

相当于执行 `npm config get prefix` 返回 Node 安装目录的 prefix。


## npm root

返回包的 `node_modules` 目录。

### 返回当前包的 node_modules 路径

```shell
➜  pkg git:(master) ✗ npm root
/Users/xxxx/Desktop/Study/pkg/node_modules
```


### 返回全局 node_modules 路径

```shell
➜  pkg git:(master) ✗ npm root -g
/Users/xxxx/.nvm/versions/node/v18.18.2/lib/node_modules
```

### 在 shell 脚本中使用

这在一些 `shell` 脚本中调用 `node_modules` 命令很有用
```shell
#!/bin/bash
bin_path = $(npm root);
$bin_path/.bin/command
```

## npm ping

ping Registry.

```shell
➜  ~ npm ping
# npm notice PING <https://registry.npmjs.org/>
# npm notice PONG 443ms
```

使用 `--registry` 参数可以 ping 自定义的注册表

```shell
npm ping --registry <https://registry.custom.org>
```


## npm doctor

运行 npm 诊断程序。
```shell
npm doctor
```

**主要诊断以下几点：**

- 文件或目录的权限检查
    - 缓存文件目录的写入权限。
    - 日志文件目录的写入权限。
- npm / node 的版本
- 执行 `npm ping`
- git 是否可执行
- 全局 `.bin` 目录是否存在
- 全局的 `node_modules` 目录是否存在
- 验证 cache 缓存文件。

## npm bugs

打开包 `package.json#bugs` 字段定义的地址。
```shell
npm explore vue -- npm bugs;
```

## npm docs

打开包 `package.json#homepage` 字段定义的地址。
```shell
npm explore vue -- npm docs;
```
## npm repo

打开包 `package.json#repositry` 字段定义的地址。
```shell
npm explore vue -- npm repo;
```

## npm audit

查阅：[[32 × NPM CLI - audit]]

## npm hook

管理 NPM 的 WebHooks 服务。

>该服务需要将 URL Endpoint 写入到要订阅的包，因此需要登录到注册表服务。

### 列出当前用户添加的所有 hooks

```shell
npm hook ls
```

### 为包添加 hook

```shell
npm hook add lodash https://www.example.subscribe.com/api "my-shared-sceret"
```

### 为范围添加 hook：

```shell
npm hook add @vue  <https://www.example.subscribe.com/api> "my-shared-sceret"
```

### 根据 hook 的 ID 删除 hook：

```shell
npm hook rm 1hukton1
```

### 更新现有的 hook：

```shell
npm hook update 1hukton1 ttps://www.example.subscribe.com/api "my-shared-sceret-2"
```

## npm config

查阅：[[31 × NPM CLI - config]]

## npm diff

查阅：[[30 × NPM CLI - diff]]

## npm link

查阅：[[28 × NPM CLI - link]]

## npm exec

查阅：[[29 × NPM CLI - exec]]

## npm cache

管理 NPM 的本地缓存。
缓存存储在 `~/.npm/cache` 目录中。

## npm prune

删除多余的包。
清理那些在 `./node_moduels` 目录中但又不存在于依赖关系树上的包。
```shell
npm install vue;
npm pkg delete dependencies.vue 

npm prune
```

## npm dedupe

重复数据删除。通过依赖提升来减少重复的包，等同于 `hoisted` 安装策略。
```shell
npm i --install-strategy nested;

npm dedupe
```

## npm owner

管理包所有者。
会发送邮件让 `example-user` 用户确认。
```shell
npm owner add example-user date-toolkit # 将用户添加到包的管理者中。
```

## npm access

设置包的访问控制。

## npm explore

进入到安装包的根目录，并执行 shell 命令。
```shell
npm i vue;
npm explore vue -- npm docs
npm explore vue -- npm run build
```

## npm query

检索依赖树，支持 CSS 选择器风格：
```shell
npm query "*" # 查找所有依赖
npm query ":root > *" # 只查找顶层依赖
npm query ":root > .dev" # 查找 dev 依赖类型，例类似的还有 .prod ｜.peer
npm query ":empty" # 查找没有其它依赖的依赖。
npm query "[name=lodash]" # 属性选择器
npm query "[name=lodash]:semver(^3)" # 检索 lodash 中 3.x.x 版本。
```

> 更多用法，参见：https://docs.npmjs.com/cli/v9/using-npm/dependency-selectors

## npm rebuild

重新构建一个包。
它主要会产生以下操作：

**执行 `install` 生命周期钩子：**

```shell
npm preinstall
npm install
npm postinstall
```

**重新创建 `bin` 程序的链接：**

```shell
rm -rf ./node_modules/.bin
npm rebuild;
```

`npm rebuild` 命令主要用于在更新 node 版本后，重新对附加的所有 C++ 组件进行编译。 对于一些原生模块，它们通常会在 `install` 钩子中调用 `node-gyp` 来编译当前平台的可执行文件。

>`node-gyp` 是一个跨平台的命令行工具，用于编译 Node.js 原生扩展模块。

>原生模块（Native Modules），在 Node.js 的上下文中，指的是那些使用 C 或C++ 等编程语言编写，并且需要编译成机器码才能在特定平台上运行的模块。这些模块通常会提供一些 JavaScript 无法直接实现的功能，比如直接与底层操作系统交互、执行密集型 CPU 操作或访问特定的硬件资源。

## npm restart

重新启动一个包。

如果 `package.json` 的 scripts 字段有 `restart` 命令，执行 `npm restart` 命令，则执行以下生命周期脚本：
```shell
npm prerestart
npm restart
npm postrestart
```

如果脚本中没有 `restart` 命令，执行该命令，则会调用以下生命周期内的脚本命令：
```shell
npm prerestart
npm prestop
npm stop
npm poststop
npm prestart
npm start
npm poststart
npm postrestart
```

## npm star

收藏喜欢的包。
```shell
npm star vue
```

## npm stars

查看收藏的包。
```shell
npm stars
```

也可以查看指定用户收藏的包
```shell
npm stars soda 
```

## npm unstar

移除收藏的包
```shell
npm unstar react
```

## npm shrinkwrap

创建 `npm-shrinkwrap.json` 文件，或者将已存在的 `package-lock.json` 重命名为 `npm-shrinkwrap` 。
```shell
npm shrinkwrap
```

## npm org

管理组织。

## npm team

管理组织团队和团队成员。