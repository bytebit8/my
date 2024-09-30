返回“包(依赖)”在项目完整依赖图中的“依赖链信息”。

## 常用别名

```shell
npm why
```

## 返回包的依赖链

```shell
➜  pkg git:(master) ✗ npm explain vue
vue@3.5.10                                            # 1️⃣
node_modules/vue                                      # 2️⃣
  peer vue@"3.5.10" from @vue/server-renderer@3.5.10  # 3️⃣
  node_modules/@vue/server-renderer                   
    @vue/server-renderer@"3.5.10" from vue@3.5.10     # 4️⃣
  vue@"^3.5.9" from pkg-1@1.0.0                       # 5️⃣
  packages/pkg-1
    pkg-1@1.0.0
    node_modules/pkg-1                                # 6️⃣       
      workspace packages/pkg-1 from the root project  
  vue@"^3.5.10" from pkg-2@1.0.0                      # 7️⃣
  packages/pkg-2
    pkg-2@1.0.0
    node_modules/pkg-2
      workspace packages/pkg-2 from the root project
```

- 1️⃣ 2️⃣ 依赖的名称与安装位置。
- 3️⃣ `vue@"3.5.10` 作为 `@vue/server-renderer@3.5.10` 等同依赖。
- 4️⃣ 既然是等同依赖，那么 `@vue/server-renderer@3.5.10` 也是 `vue@"3.5.10` 的生产依赖。
- 5️⃣ `vue@"3.5.10` 是包 `pkg-1@1.0.0` 的生产依赖。
- 6️⃣ 包 `pkg-1@1.0.0` 被链接到 `node_modules/pkg-1` 下。
- 7️⃣ `vue@"3.5.10` 是包 `pkg-2@1.0.0` 的生产依赖。

`npm explain` 与 `npm ls <package-name>` 返回值非常类型，只是 `npm ls` 侧重于返回依赖在项目完整依赖树中的位置：
```shell
➜  pkg git:(master) ✗ npm ls vue
pkg@1.0.0 /Users/xxxx/Study/pkg
├─┬ pkg-1@1.0.0 -> ./packages/pkg-1
│ └─┬ vue@3.5.10
│   └─┬ @vue/server-renderer@3.5.10
│     └── vue@3.5.10 deduped
└─┬ pkg-2@1.0.0 -> ./packages/pkg-2
  └── vue@3.5.10 deduped
```

## 以 JSON 格式返回依赖链信息

以 JSON 格式返回依赖在项目完整依赖图中的依赖链信息，更加可读与程序化处理。
```shell
npm why vue --json
```


## 工作区支持

- 默认返回依赖在整个工作区中的依赖链信息。
- `-w` 参数返回依赖在指定子包中的依赖链信息。
- `-ws` 参数返回依赖在工作区中所有子包内的依赖链信息。

## 常用参数
| 参数       | 说明         |
| -------- | ---------- |
| `-w`     | 废弃指定的子包。   |
| `-ws`    | 废弃工作区所有子包。 |
| `--json` | JSON 格式输出。 |
