
漏洞检查与审计签名。

## 漏洞检查

`npm audit` 命令会完整的检查本地依赖树是否存在安全漏洞，并根据严重等级来生成漏洞报告。从高往低依次为：`critical` → `high` → `moderate` → `low` 。

```shell
npm audit 
npm audit --json # 使用 JSON 格式返回漏洞信息。
```

## 自定义漏洞等级

自定义检查漏洞等级可以使用 `--audit-level` 命令选项。

```shell
npm audit --audit-level high
```

## 自动修复漏洞

如果存在漏洞且有可用的更新，那么可以使用 `npm audit fix` 命令自动进行修复。
```shell
npm audit fix
```

其底层是使用 `npm install` 命令来安装修复后的依赖版本。

## 审计签名

使用注册表的公钥和包的签名来验证下载 `tarball` 的完整性：
```shell
npm audit signatures
```

## 工作区支持

- 默认只针对当位置的包生效（根包/子包）。
- 使用 `-w` 参数可以只对指定的子包生效。
- 使用 `-ws` 参数可以对所有工作区子包生效（不含根包）。

## 常用参数
| 参数              | 说明                 |
| --------------- | ------------------ |
| `--audit-level` | 自定义检查的漏洞等级。        |
| `--json`        | 以 JSON 格式输出。       |
| `--dry-run`     | 调试日志的方式运行，不产生实体修改。 |
| `-workspace`    | `-w`               |
| `-workspaces`   | `-ws`              |