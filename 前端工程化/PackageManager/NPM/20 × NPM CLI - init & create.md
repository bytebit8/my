
## 别名

```shell
npm create -y
```

## 初始化 npm 包

创建包描述文件 `package.json`:
```bash
npm init                # 交互式创建 package.json 文件
npm init -y             # 非交互式创建 package.json 文件
npm init --scope @scope # 创建具有作用域的 package.json 文件
```

## 调用生成器程序

`init` 命令底层基于 `npm exec` 命令。在传入包名时，可以直接调用目标包的 `bin` 程序，同时它还默认支持省略 `create-*` 为前缀的包名。这对于一些充当脚手架的包非常有效，`init` 命令可以直接执行其生成器程序（initializer）快速创建(包)项目。

```shell
npm create react-app my-app # npm exec create-react-app my-app
npm create vue # npm exec create-vue
```


## 工作区支持

创建并添加“包“到工作区中。

以 `app-mono` 项目为例，其目录结构如下：
```text
app-mono
	├── package-lock.json
	├── package.json
	└── packages
```

使用 `-w` 参数创建并添加包到工作区(Workspaces)
```shell
npm init -y -w ./packages/app1 --scope=app-mono
npm init react-app . -w ./packages/app2
```

此时再查看工作区目录结构：
```text
app-mono
  ├── node_modules
	│   └── @app-mono
	├── package-lock.json
	├── package.json
	└── packages
	    ├── app1
	    └── app2
```

此时顶层包的 `package.json` 文件 `workspaces` 字段内容：
```json
"workspaces": [
  "packages/app1",
  "packages/app2"
]
```


## 常用命令选项
| 参数                 | 说明             |
| ------------------ | -------------- |
| `-y`               | `--yes`        |
| `--scope`          | 创建具有作用域的包。     |
| `-w` ｜`—workspace` | 创建包并关联工作区。     |
| `--loglevel`       | 日志等级。`verbose` |
| `--timing`         | 计时信息。          |