对项目打 tarball。相当于 `npm publish` 命令只打包未上传的状态。

```shell
npm pack
```

## 惰性打包

如果只是了调试打包的产物，而不用输出实际的 tarball，可以使用 `--dry-run` 命令选项进行惰性打包。
```shell
npm pack --dry-run
```

为了更好的查看 tarball 的产物信息，可以以 JSON 格式输出：
```shell
npm pack --dry-run --json
```

## 将 tarball 输出到指定位置

```shell
npm pack --pack-destionation ../
```

## 工作区支持

- 默认只针对当前包打 tarball（根包或子包）
- `-w` 参数可以对指定的子包打 tarball。
- `-ws` 参数可以对工作区中所有子包打 tarball（不含根包）

## 常用参数
| 参数                    | 说明                |
| --------------------- | ----------------- |
| `--dry-run`           | 惰性打包              |
| `--json`              | JSON 格式输出。        |
| `--pack-destionation` | 输出 tarball 到指定目录。 |
| `-w`                  | 废弃指定的子包。          |
| `-ws`                 | 废弃工作区所有子包。        |
