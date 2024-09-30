关于“包”与模块的基础知识可以阅读：[[npm 包的基础概念]]
## 创建 package.json

向 NPM Registry 提交的 npm 包必须要包含一个 `package.json` 文件。创建 `package.json` 文件的主要方式如下：
- 手动创建
- npm CLI 创建
- 程序化创建

使用命令行创建：
```shell
npm init # 以交互的方式创建
npm init --yes # 默认创建，以当前的目录名称作为包名。
```

编写脚本程序，调用官方提供的 `@npmcli/package-json` 软件包来创建：
```js
import packageJson from "@npmcli/package-json";

(async function () {
  const pkg = await packageJson.load(process.cwd() + "/pkg", { create: true });
  await pkg.normalize();

  pkg.content.name = "cpkg";
  pkg.content.version = "1.0.0";
  pkg.content.author = "author author@email.com";
  pkg.content.description = "create package.json";

  await pkg.save();
})();
```

>[!tip]
>NPM 官方推荐使用 `init-package-json` 软件包。它是基于 `@npmcli/pacakge-json` 封装的以交互的方式来创建 `package.json` 文件的工具。

 在 `package.json` 文件中，`name` 与 `version` 字段是必须的(required)：
 - `name`: npm 软件包的名称，必须是由安全的 URI 字符集组成。
 - `version`: npm 软件包的版本，遵循语义化版本控制[^1]，默认从 `1.0.0` 起始。
 
 除了必要的字段外，`package.json` 文件还建议添加以下几个字段：
 - `author`: 包的作者名称，可以顺带加上邮箱地址，用空格隔开。
 - `keywords`: 包的关键字。
 - `description`: 包的描述说明。

```json
{
  "name": "pkg",
  "version": "1.0.0",
  "author": "xxxx xxx@163.com",
  "keywords": ["npm package", "package.json"],
  "description": "Create an npm package for learning",
}
```


## 包的自述文件

npm 包本身也是一个项目，因此包的自述文件就是普通项目的 `README.md` 文件。

## 包的忽略配置文件

NPM 支持使用 `.gitignore` 配置文件来忽略项目中的特定文件或目录，从而避免将文件误发布到注册表中。同时 NPM 还提供了官方专属的 `.npmignore` 配置文件，其优先级要高于 `.gitignore` 配置文件。

>忽略项目的中的文件或目录以避免误发布到注册表中，这对于一些高度敏感的本地配置文件而言非常有用。

虽然 NPM 支持 Git 的 `.gitignore` 配置文件，且官方还提供了专属的 `.npmignore` 配置文件，但是请**尽量避免使用它们**，因为我们有更好的方案。

>[!warning]
>
>1. `.gitignore` 配置文件并不是专门为 NPM 服务。有关 NPM 包的发布配置可能会与版本控制程序的提交配置冲突。
>2. 当 `.npmignore` 与 `.gitignore` 配置文件同时存在时，会增加开发者的理解负担。这潜在增加了误提交隐私文件的风险性。
>3. 每次新增或删除文件或目录都要频繁的对忽略配置文件进行更新，这无疑提高了 NPM 包发布的维护成本
>4. `.npmignore` 配置文件还有多个隐含着的条件限制，例如 `package.json` 文件的 `files` 字段以及 `bin` 字段等。

对比主动忽略的方案，主动添加的方案将更加安全(成功提交文件固然重要，但是保证提交安全的文件将更加的重要)和具有扩展性。通过配置 `package.json` 文件的 `files` 字段，可以只将匹配到的文件或目录提交到注册表中：
```json
{
  "name":"pkg",
  "version":"1.0.0",
  "files":[
      "src",
      "!src/**/*.test",
      "types",
      "README.md"
  ]
}
```

>`package.json` 文件的 `files` 字段相当于充当了“白名单”的角色。

## 管理包的多个注册表

可以使用 npm CLI 命令或 npm CLI 的配置文件 `.npmrc` 来管理多个注册表。

`npm config` 命令：
```shell
npm config list
npm config set registry  https://registry.npmjs.org
npm config set @scopeName https://xxx.registry.npmjs.org

npm config list -l

npm config rm @scopeName;
```

`.npmrc` 配置文件：
```text
registry=https://registry.npmjs.org/
@scopeName:registry=https://xxx.registry.npmjs.org/
```

当安装或发布 npm 包时，对于作用域包，npm CLI 会自动根据包名中的作用域（scope）命来自动选择对应的注册表。如果没有找到，则使用默认的公共注册表。

对于非作用域包，则默认使用公共注册表。


## 创建和发布无作用域包

```shell
npm init -y
npm publish
```

## 创建和发布作用域包到私有

>[!note]
>“私有包”必须是付费的账户或组织才能使用。

```shell
npm init --scope @scope-test --y
npm run publish
```

>作用域包默认的可访问性为私有。
>对于用户范围的包，请将作用域名称替换为用户名。
>对于组织范围的包，请将作用域名称替换为组织名。

## 创建和发布作用域包到公共

>[!note]
>因为作用域包默认为“私有”，因此如果需要发布到公共，则必须使用 `--access public`参数。

```shell
npm init --scope @scope-test --y
npm publish --access public
```

## 创建和发布带有 tag 的包

对比 NPM 默认只有一个 `latest` 的 dist tag；使用 `publish` 命令的 `--tag` 参数，我们可以发布自定义的分发标签。

```shell
npm init -y
npm publish --tag beta
```

假设 npm 包的当前版本为 `1.0.1` ，发布后，在 NPM 注册表中该版本号对应的 dist tag 便是 `beta`，它相当于是版本号的别名，但更加的可读，用户可以通过 `install` 命令来安装 `beta` 版本对应的 `1.0.1` 版本的软件包。

```shell
npm i beta
```

---
[^1]: [[语义化版本控制和分发标签]]