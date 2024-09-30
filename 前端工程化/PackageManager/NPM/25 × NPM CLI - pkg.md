
管理包的 `package.json` 文件

## 完整返回 `package.json` 

```shell
npm pkg get
```

## 读取 package.json 字段值

```shell
npm pkg get name
npm pkg get name version
npm pkg get scripts.test
```

## 设置 pakcage.json 字段直

```shell
npm pkg set description="This is description."
npm pkg set scripts.compress="node ./scripts/compress.js"
```

## 删除 package.json 字段

```shell
npm pkg delete scripts.compress
```

## 自动修复 package.json 错误

给定一个错误版本号的 `package.json`
```json
{
  "name":"pkg",
  "version":"1.0.02"
}
```

执行修复命令后：
```shell
npm pkg fix
npm pkg get version #1.0.2
```

## 工作区支持

- 默认只会管理所处位置包的 `package.json` 文件。
- `-w` 参数可以管理指定子包的 `package.json` 文件。
- `-ws` 参数可以管理工作区所有子包的 `package.json` 文件。

## 常用参数
| 参数       | 说明         |
| -------- | ---------- |
| `-f`     | 强制执行。      |
| `--json` | JSON 格式输出。 |
| `-w`     | 废弃指定的子包。   |
| `-ws`    | 废弃工作区所有子包。 |
