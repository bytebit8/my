## 列出项目的直接依赖

默认列出项目的所有直接依赖，可以通过 `--depth` 选项来列出传递依赖。

```shell
pkg@1.0.0 /Users/xxxx/Desktop/pkg
├─┬ pkg-1@1.0.0 -> ./packages/pkg-1
│ └── vue@3.5.9
└─┬ pkg-2@1.0.0 -> ./packages/pkg-2
  └── lodash@4.17.21
```

## 列出项目的所有依赖

包括项目的直接依赖与传递依赖。

```shell
npm ls --all
```

## 查看项目的指定依赖

从项目完整的依赖树中查找指定的依赖:

```shell
➜  pkg git:(master) ✗ npm ls vue
pkg@1.0.0 /Users/xxxx/Desktop/pkg
└─┬ pkg-1@1.0.0 -> ./packages/pkg-1
  └─┬ vue@3.5.9
    └─┬ @vue/server-renderer@3.5.9
      └── vue@3.5.9 deduped
```

>可以发现，依赖 `vue` 在项目的完整依赖树中出现了两次。

`deduped` 表示重复的依赖。这里是因为 server-renderer 有设置同等依赖。

## 控制依赖树的深度

```shell
➜  pkg git:(master) ✗ npm ls --depth 1
pkg@1.0.0 /Users/xxxx/Desktop/pkg
├─┬ pkg-1@1.0.0 -> ./packages/pkg-1
│ └─┬ vue@3.5.9
│   ├── @vue/compiler-dom@3.5.9
│   ├── @vue/compiler-sfc@3.5.9
│   ├── @vue/runtime-dom@3.5.9
│   ├── @vue/server-renderer@3.5.9
│   ├── @vue/shared@3.5.9
│   └── UNMET OPTIONAL DEPENDENCY typescript@*
└─┬ pkg-2@1.0.0 -> ./packages/pkg-2
  └── lodash@4.17.21
```

## 只列出符号链接的依赖

```shell
➜  pkg git:(master) ✗ npm ls --link
pkg@1.0.0 /Users/xxxx/Desktop/pkg
├── pkg-1@1.0.0 -> ./packages/pkg-1
└── pkg-2@1.0.0 -> ./packages/pk
```

## 只列出依赖的路径

```shell
➜  pkg git:(master) ✗ npm ls --depth 1 -p
/Users/xxxx/Desktop/pkg
/Users/xxxx/Desktop/pkg/node_modules/pkg-1
/Users/xxxx/Desktop/pkg/node_modules/pkg-2
/Users/xxxx/Desktop/pkg/node_modules/vue
/Users/xxxx/Desktop/pkg/node_modules/lodash
/Users/xxxx/Desktop/pkg/node_modules/@vue/compiler-dom
/Users/xxxx/Desktop/pkg/node_modules/@vue/compiler-sfc
/Users/xxxx/Desktop/pkg/node_modules/@vue/runtime-dom
/Users/xxxx/Desktop/pkg/node_modules/@vue/server-renderer
/Users/xxxx/Desktop/pkg/node_modules/@vue/shared
```

## 工作模式

**全局模式:**
使用 `-g` 或 `--global` 命令选项，列出全局依赖的依赖树结构：

```shell
npm ls -g
```

**本地模式：**
```shell
npm ls
```

## 工作区支持

- 默认列出整个项目的依赖。
- 若当前位置位于某个子包，则只会列出子包的依赖。
- `-ws` 参数可以查看工作区的所有子包依赖关系树（不含根包）。
- `-w` 参数过滤子包，可以查看指定子包的依赖信息（不含根包）。

```bash
npm ls                           # 列出整个项目的依赖 或 当前子包的依赖。
npm ls -ws                       # 列出所有工作区中的依赖。
npm ls lodash -ws                # 查看所有工作区中指定的依赖。
npm ls -w packages/app1          # 列出指定工作区中的依赖。
npm ls lodash -w packages/app2   # 查看指定工作区中的指定的依赖。
```

## 常用参数
| 参数        | 取值         | 说明                              |
| --------- | ---------- | ------------------------------- |
| `-g`      | `--global` | -                               |
| `-a`      | `--all`    | -                               |
| `--depth` | level      | 控制依赖树层级输出。                      |
| `-p`      | -          | 列出安装包的路径。                       |
| `--json`  | -          | 以 JSON 格式输出安装包的信息。              |
| `--omit`  | dev        | optiona                         |
| `--link`  | -          | 仅显示链接的包（即使用 `npm link` 命令链接的包）。 |