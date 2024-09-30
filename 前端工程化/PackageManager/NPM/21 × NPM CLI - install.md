## 命令别名

```shell
npm i
npm add
```

## 安装指定的包

- 安装新的依赖，更新 `package.json` 与 `package-lock.json` 文件
- 安装已存在依赖
	- 依赖满足版本范围语法下的最新版本，跳过依赖安装。
	- 依赖不满足版本范围语法下的最新版本
		- 更新依赖
		- 更新 `package.json` 与 `package-lock.json` 文件

```shell
npm i lodash 
npm i lodash@latest
npm i lodash@4.1.20
npm i lodash -g
npm i lodash --save-dev
npm i lodash --save-peer
```


## 安装所有依赖

使用 `package.json` 或 `package-lock.json` 安装项目的所有依赖：

- 没有 `package-lock.json` 文件：
	- 解析 `package.json` 依赖信息，安装所有依赖。
	- 若当前依赖存在于 `node_modules` 目录：
		- 依赖满足版本范围语法下的最新版本，则跳过当前依赖的安装
		- **依赖不满足版本范围语法，更新依赖，重新安装依赖**
	- 创建 `package-lock.json` 文件。
- 存在 `package-lock.json` 文件：
  **完全按照依赖锁文件的依赖信息安装依赖。**

```shell
npm i
```

## 安装包的类型

```bash
npm install ../pkg
npm install ../pkg.tar.gz
npm install <http://example.com/pkg.tar.gz>
npm install <https://github.com/xxxx/xx-xxx.git>
```

>不建议从不受信任的注册表中心下载包。

## 安装模式

全局安装，命令参数：`-g` 或 `--global`。
- 包被下载到 `$(prefix)/lib/node_modules` 目录。
- 包的 `bin` 程序被创建符号链接到 `$(prefix)/bin` 目录。

```shell
npm i loadsh -g;
```

本地安装。
- 包被下载到当前项目的 `./node_modules` 目录。
- 包的 `bin` 程序被链接到 `./node_modules/.bin` 目录。

具体示例：[[#安装指定的包]]


## 安装策略

**递归嵌套安装 (npm 1 - 2)**

```shell
npm i --install-strategy nested
```

**依赖提升（ npm 3.x）**

```shell
npm i --install-strategy hoisted
```

**依赖浅层提升**

```shell
npm i --install-strategy shallow
```

兼得依赖提升（重复数据删除）和解决幽灵依的浅层安装。

**符号链接 (npm 9.x)**

通过创建符号链接来安装依赖，共享重复数据，类似于 pnpm 的特性。
```shell
npm i --install-strategy linked
```

## 工作区支持

- 如果当前位置位于“根包”， 执行命令则会为根包安装依赖。
- 如果当前位置位于“子包”，执行命令则会为当前包安装依赖。
- 使用 `--workspaces` 参数可以将依赖安装到所有工作区（不会安装到根包）
- 使用 `-w` 参数可以过滤工作区，将依赖安装到指定的工作区中。

```bash
npm i date-toolkit                # 安装到根包或子包中。
npm i lodash --workspaces         # 安装依赖到所有的工作区（不含根包）。
npm i glup -w packages/app1 -D    # 安装依赖到指定的工作区。
```

## 常用命令参数
| 参数                    | 说明                                                   |
| --------------------- | ---------------------------------------------------- |
| `-g`                  | `--global`                                           |
| `-D`                  | `--save-dev`                                         |
| `-O`                  | `--save-optional`                                    |
| `-B`                  | `--save-bundle`                                      |
| `--save-peer`         | 添加到 `package.json` 的 `peerDependencies` 中。           |
| `-E`                  | `--save-exact`                                       |
| `--dry-run`           | 以日志的方式运行安装程序，不会实际安装任何东西（不会发生实体更改）。                   |
| `--no-save`           | 安装依赖但是不会更新 `package.json` 文件。                        |
| `--package-lock-only` | 只更新 `package.json` 与 `package-lock.json` 文件，并不会安装依赖。 |
| `--production`        | 只安装 `dependencies` 中的依赖。                             |
| `-f`                  | `--force`                                            |
| `--install-strategy`  | 安装策略                                                 |
| `-w`                  | 将依赖安装到指定的工作区。                                        |
| `-ws`                 | 将依赖安装到所有工作区。                                         |
| `--loglevel`          | 日志等级。`verbose`                                       |
| `--timing`            | 计时信息。                                                |
| `--no-audit`          | 安装依赖时不执行漏洞检查与签名审计。                                   |

>[!NOTE] 
>强制安装模式
>- 忽略本地缓存中的依赖。
>- 忽略 `engines` 字段不匹配的包。
>- 忽略 `peerDependencies` 字段冲突的包。