## 应用场景

以干净可预测的方式安装依赖。用于自动化环境（测试平台、持续集成）。
```yaml
name: Install Package In The Github Actions.
on:
  push:
    branches:
      - main
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18.x'
          registry-url: '<https://npm.pkg.github.com>'
      - run: npm ci
```

## 对比 npm install

- 不能安装指定的依赖。
- 只能使用 `package-lock.json` 的依赖信息安装依赖。没有 `package.json` 与 `package-lock.json` 文件时退出安装。
- 永远不会更新 `package.json` 与 `package-lock.json` 文件。
- 总是会删除 `node_modules` 目录重新安装。

 基于 `package-lock.json` 安装依赖可以更好的保持依赖环境的一致性。因为使用 `package.json` 在安装的过程中，可能会更新依赖。

## 工作区支持

- `npm ci` 命令默认会安装项目所有的依赖（根包 + 所有子包）
- `-ws` 参数可以安装所有子包的全部依赖（但不会安装根包的依赖）。
- `-w` 参数可以安装指定子包的全部依赖。

```shell
npm ci                     # 安装根包的全部依赖。
npm ci -ws                 # 安装所有子包的全部依赖。
npm ci -w packages/app2    # 安装指定子包的全部依赖。
```