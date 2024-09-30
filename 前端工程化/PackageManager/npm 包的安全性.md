## 概述

NPM 的安全性问题从不是 NPM 本身（指 npm CLI 或 Registry），而是那些发布到 NPM 上被共享的 npm 包。

npm 包的安全性问题大致可以分为两类：**“npm 供应链攻击”** 和 **“npm 包本身攻击”。** 攻击者本质都是利用了现代软件开发对第三方“库”或“模块的”广泛依赖，以间接或直接的形式通过包的依赖关系向目标项目注入恶意代码。

>安装一个普通的 npm 包会引入79个第三方包和39个相关包的维护人员的隐式信任，从而潜在存在一个惊人的受攻击风险。

**npm 供应链攻击**
操纵、篡改或伪造 npm 包来注入恶意代码。常见的攻击手段有：
- 来源攻击：
	利用“社会工程学攻击”窃取包拥有者的身份认证信息，发布带有恶意代码的版本。
	针对此类攻击，请参阅：[[#生成出处证明]]
	一个很知名的案例：[xz backdoor](https://snyk.io/blog/the-xz-backdoor-cve-2024-3094/)
- 开源软件贡献攻击：
	通过贡献代码攻击者向 lockfiles 文件注入恶意依赖。
	针对 `lockfiles` 的攻击可以考虑：[[#lockfile-lint]]
- 依赖混淆攻击
	故意创建一个名称和广泛依赖的包接近的恶意包，诱导用户下载安装。
	另一种攻击场景就是攻击同名的私有包。针对那些具有私有注册中心的组织，当依赖的包在公共注册表中存在同名且公共注册表拥有最新版本时，安装依赖，会默认安装公共注册表的最新版本。
	针对此种攻击，可以使用 `Verdaccio` 并结合带有作用域的包。


**npm 包的本身攻击**
主动创建带有恶意攻击代码的 npm 包。在此种攻击类型下，开源软件的拥有者们也不在可信。
* NPM Script Hooks 攻击
  利用 NPM 执行命令时会自动执行前置/后置钩子或生命周期脚本，来嵌入恶意代码或后门程序，进行持久性的攻击。
  针对此种攻击，尽可能开启 `--ignore-scripts` 来执行 script hooks。
- 跨站脚本攻击（XSS）
	如果 npm 包用于前端开发，攻击者可能会在包中嵌入恶意的 JavaScript 代码，导致用户浏览器执行恶意操作。
- 拒绝服务攻击（DoS）：
	攻击者可以通过发布恶意包来引入性能瓶颈或无限循环，从而导致应用崩溃或变慢。
	知名案例就是 `Marak` 对其拥有的开源项目 `colors.js` 引入的无限循环。
	https://snyk.io/blog/open-source-npm-packages-colors-faker/

>[!WARNING]
>npm 包从开发、上传、构建、下载、安装到执行的每个关键节点都会受到攻击。


## 锁定依赖版本

在自动化流程中安装依赖，尽可能使用 `npm ci` 它会严格的按照锁定的依赖版本安装，尽量减少因为依赖更新而产生的攻击。

## lockfile-lint 

https://github.com/lirantal/lockfile-lint

## 漏洞检查

执行 `npm audit` 命令可以生成一份漏洞检查报告。报告主要包含漏洞的等级、类型、位置以及相关修复的指引。

>[!note]
>只会检查包的 `dependencies`、`devDependencies`、`bundledDependencies` 、`optionalDependencies` 等依赖字段，不检查 `peerDependencies`。因为这是宿主项目的依赖，而非当前包的直接或间接依赖。

[[前端工程化/PackageManager/assets/fcee96042e1d46161185b8d3f7bfcff8_MD5.jpeg|Open: Pasted image 20240924153718.png]]
![[前端工程化/PackageManager/assets/fcee96042e1d46161185b8d3f7bfcff8_MD5.jpeg]]

漏洞按照严重程度（Serverity）可以分为以下几个等级：

| Serverity | Description |     |
| --------- | ----------- | --- |
| Critical  | 非常重要，需要立即修复 |     |
| High      | 重要，尽快解决     |     |
| Moderate  | 有时间时解决      |     |
| Low       | 由您自行决定何时解决  |     |

1. 严重程度（Serveity）右侧的描述文案是对漏洞的介绍，常见的比如：
	- `Prototype pollution` - 原型链污染
	- `Denial of service` - Dos 攻击
2. `patched` 字段表示的是具有修补程序的包版本范围。
3. `Path` 字段则是返回存在漏洞包在依赖关系链路中的位置。

若存在漏洞的包过多，报告的内容过长，可以执行以下命令，将报告内容以 JSON 的格式存储到文件中。
```shell
npm audit --json > npm_audit_report.txt
```

如果发现的漏洞有可用的自动更新，你可以执行：
```shell
npm audit fix
```

对于需要手动修复的漏洞，可以根据报告建议的信息进行操作。
[[前端工程化/PackageManager/assets/507ddf881f5e169562b17f83b8299ac6_MD5.jpeg|Open: Pasted image 20240924153814.png]]
![[前端工程化/PackageManager/assets/507ddf881f5e169562b17f83b8299ac6_MD5.jpeg]]

如果没有发现安全漏洞，执行 `npm audit` 命令则会得到以下结果：
```shell
➜  pkg npm audit
found 0 vulnerabilities
```

但请记住，这只是一时的，因为漏洞数据库是动态更新的。对此，我们建议定期运行`npm audit` 以获取最新的安全审计报告。

在安装依赖时 `npm audit` 会被自动执行，若想关闭这一默认行为，可以执行下面命令：
```shell
npm install example-package-name --no-audit # 单个软件包
npm set audit false  # 全局关闭
```

或许，另一种更有效的方式是将漏洞检查集成到自动化流程中。当在程序或脚本中运行 `npm audit` 命令时，如果有漏洞则返回非 `0` 错误码退出，没有漏洞则返回 `0` 错误码退出。
```yaml
audit_dependencies:
  stage: audit
  script:
    - npm audit --audit-level high
  allow_failure: false
```

若需要控制检查漏洞的等级可以使用 `--audit-level` 参数：
```shell
npm audit --audit-level high
```

## 验证 ECDSA 注册表签名

执行下面命令，可以使用包在注册表中的签名和公钥来验证下载包的完整性，防止下载到被篡改的包。
```shell
npm audit signatures
```

- 包的完整性(`Integrity`) Hash 会在发布包时生成并一同提交到注册表中。
- 包的签名(`Signatures`) 则由注册表服务使用密钥结合包的相关信息生成。

npm 会使用密钥对以下格式的包信息生成“签名”：
```text
${package.name}@${package.version}:${package.dist.integrity}
```

生成后的签名与对应的公钥会一同提交到注册表的 `dist.signatures` 字段中：
```json
"dist": {
	....
  "signatures": [
    {
      "keyid": "SHA256:jl3bwswu80PjjokCgh0o2w5c2U4LhQAE57gj9cz1kzA",
      "sig": "MEYCIQD4FwNiAFaIKwLB4ewAs1slmZ/DSEXQLviAw0DNY6zPSQIhANpX1J2Q0MhOrmNue0DqgXPP9GjT3n8lfHcsKPkIK/Us"
    }
  ],
}
```

公钥的获取， 也可以通过以下链接获取（仅限 npm 注册表的公钥）：
```shell
➜  ~ curl https://registry.npmjs.org/-/npm/v1/keys
{"keys":[{"expires":null,"keyid":"SHA256:jl3bwswu80PjjokCgh0o2w5c2U4LhQAE57gj9cz1kzA","keytype":"ecdsa-sha2-nistp256","scheme":"ecdsa-sha2-nistp256","key":"MFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAE1Olb3zMAFFxXKHiIkQO5cJ3Yhl5i6UPp+IhuteBJbuHcA5UogKo0EWtlWwW6KSaKoTNEYL7JlCQiVnkhBktUgg=="}]}
```

当开发者在安装包时 npm CLI 会通过公钥来对注册表中包的签名进行解密，并用解密后的结果与包的完整性 Hash 进行比对，比对成功则说明安装包没有被篡改。

>[!note]
>密钥是存储在 NPM 服务器中的，几乎不可能有泄露的风险，对应的公钥则是完全公开的，因此加密只能由 NPM 官方服务进行，而解密则所有客户端均可。其好处就是攻击者无法通过伪造签名来绕过包的完整性签名（如果签名是伪造的，那么公钥将无法解密）。

如果包没有签名并且包注册表支持签名，CLI 将出错。这可能意味着攻击者可能试图绕过签名验证。
```shell
audited 1640 packages in 2s

1405 packages have verified registry signatures

235 packages have missing registry signatures but the registry is providing signing keys:
```

针对此种情况可以通过向 `~/-/npm/v1/keys`  请求公共签名密钥来检查注册表是否支持签名。

## 使用 2FA 来验证包的发布

作为包的拥有者，你可以要求对包具有发布权限的用户开启 2FA 认证。这将要求用户在发布包时除了提供登录令牌外还提供 2FA 凭据。

> 在持续集成部署环境中推荐使用自动化访问令牌以跳过 2FA 验证。

[[前端工程化/PackageManager/assets/6a368677b2c0643baa64a695aeebe4d3_MD5.jpeg|Open: Pasted image 20240924153902.png]]
![[前端工程化/PackageManager/assets/6a368677b2c0643baa64a695aeebe4d3_MD5.jpeg]]

## 生成出处证明

npm 包的证明有两种：
- `Provenance Attestation` ：出处证明
- `Publish Attestation` ：发布证明
### 先决条件

“出处证明”是“发布证明”的前提条件，为 npm 包添加出处证明要满足以下先决条件：

1. 确保您的`package.json` 中 `repository` 配置是存放源码并发布的位置。
2. 使用受支持的 CI/CD 来构建 npm 包。
    1. GitHub 的 `GitHub Action` 。
    2. GitLab 的 `GitLab CI/CD` 。
3. 在 CI/CD 中使用 `npm publish --provenance` 命令向注册表发布包。

### 在 GitHub 发布带有出处证明的包

```yaml
name: Publish Package to npmjs
on:
  push:
    branches:
      - main
jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      id-token: write
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18.x'
          registry-url: '<https://registry.npmjs.org>'
      - run: npm install -g npm
      - run: npm ci
      - run: npm publish --provenance --access public
        env:
          NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
```

以上构建配置中有两个关键步骤：

1. 在构建流程中创建具有读写权限的 `ID Token` ，用于与 `OIDC` 服务进行身份验证。
2. 使用 `npm publish --provenance` 发布包。并使用 `NODE_AUTH_TOKEN` 来与 NPM Registry 进行身份验证。

> `OIDC` (OpenID Connect) ，这里的服务对象是 `Sigstore` 服务 。

### Sigstore 是什么？

Sigstore 是一个非营利的项目，旨在提升软件供应链的安全性，通过提供一个公共的、透明的和易于使用的数字签名服务，用于软件的发布和验证。

Sigstore 核心组件有以下四个：

1. **Cosign** ：用于签名和验证容器镜像的命令行工具。它简化了在构建和部署过程中对容器镜像的签名流程。
2. **Rekor** ：一个公共透明日志，它存储公共软件工件的签名记录。Rekor 使得任何人都能验证签名的存在并确保在签名后的工件未被篡改。
3. **Fulcio** ：一个证书颁发机构（CA），它为短期密钥发放证书。这些密钥可以在单次签名操作中使用，然后安全地丢弃，从而降低了长期密钥暴露风险。
4. **OpenID Connect**（认证方式）：作为一个身份层，用于检查真实性，同时允许客户端请求和接收有关经过身份验证的会话和用户的信息。

Sigstore 的工作流程如下：

1. cosign 在内存中生成一个临时公钥/私钥对。
2. 用户通过 Google 或 GitHub 等 OpenID Connect (OIDC) 提供商进行身份验证，验证电子邮件地址的所有权和先前生成的密钥的所有权。
3. 如果身份验证成功，用户将收到代码签名证书。
4. 代码签名证书发布到透明日志，以便其他人验证。
5. 用户使用代码签名证书及其密钥对工件进行签名。
6. 来自工件的签名被发布到透明日志。
7. 用于创建签名的短期代码签名凭证被销毁。
8. 可以发布已签名的工件（例如，在容器注册表上）

### 在 GitLab 发布带有出处证明的包

在 `GitLab CI/CD` 中发布具有出处证明的 npm 包：[**Example GitLab CI job**](https://docs.npmjs.com/generating-provenance-statements#example-gitlab-ci-job)

> 需要注意的是 `GitLab CI/CD` 并没有直接集成 `Sigstore` 服务，需要单独本地安装相关组件。


### npm 包的发布证明

当一个 npm 包发布并带有出处证明时，它会由 Sigstore 公共利益服务器签名并记录在公共透明日志中，NPM 注册表服务会读取并验证该软件包的签名证书，一旦校验无误便会为其生成发布证明。

当一个 npm 包既具有“出处证明”也具有“发布证明”时，在其详情页就会有个“包来源”的信息标记：

[[前端工程化/PackageManager/assets/76491bee1e809edb6c01b1963f9e033f_MD5.jpeg|Open: Pasted image 20240924153835.png]]
![[前端工程化/PackageManager/assets/76491bee1e809edb6c01b1963f9e033f_MD5.jpeg]]

[[前端工程化/PackageManager/assets/79df1bf920e20f41d76c1c13596644a6_MD5.jpeg|Open: Pasted image 20240924153852.png]]
![[前端工程化/PackageManager/assets/79df1bf920e20f41d76c1c13596644a6_MD5.jpeg]]

