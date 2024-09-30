## 访问令牌

访问令牌（Access Token）是一串十六进制字符串，用以**替代用户名/密码的另一种身份认证方式**。 访问令牌可以进行身份验证、安装（私有）包、发布包。

创建访问令牌：点击页面右上角头像，下拉菜单选择 “Access Token”，在访问令牌列表页中点击“Generate New Token” 按钮。

[[前端工程化/PackageManager/assets/ab52fc787b20985068b3503a6c4d999a_MD5.jpeg|Open: Pasted image 20240923220127.png]]
![[前端工程化/PackageManager/assets/ab52fc787b20985068b3503a6c4d999a_MD5.jpeg]]

NPM 的访问令牌有两种：
- 经典访问令牌（Classic Token）（旧版）
- 细粒度访问令牌（Granular Access Token）（推荐）

## 经典访问令牌

是 NPM 早期支持的访问令牌类型。

- **Ready-only** : 只读。只能安装（私有）包
- **Automation** : 自动化。可以安装与发布包并且可以绕过 2FA 验证，主要用于自动化环境。
- **Publish** : 发布。可以安装与发布包，如果账户开启了 2FA 验证，必须要先通过验证。

>[!note]
>自动化令牌常应用于自动化流程中。 使用 npm CLI 无法创建自动化令牌。

## 细粒度访问令牌

- **Expiration:** 可以设置过期时间
- **Allowed IP ranges:** IP 白名单。值必须是一个有效的 CIDR。
- **Packages and scopes:** 包权限设置，限制令牌可以访问的包和范围。
    - No Access
    - Ready only
    - Ready and write
- **Organizations:** 组织权限设置，授予令牌管理组织的设置、成员、团队等。
    - No Access
    - Ready only
    - Ready and write

> 细粒度访问令牌与“自动化”令牌一样不强制开启 2FA 验证。

>[!note]
>CIDR (Classless Inter-Domain Routing) — 无类别域间路由，也叫无类别编址。它是一种用来创建可变子网掩码的技术。采用斜杠表示法，用来快速表示 IP 地址中网络地址与主机地址的长度，从而更好的控制一个网络中可用 IP 主机的数量。 例如 `192.68.0.1/24` 24 表示网络地址的长度，剩下的 8 位则是主机地址，2^8 = 256，又因为同一网段下的 `192.168.0.0` 与 `192.168.0.255` （广播地址）属于保留地址，所以实际可用的只有 254 个主机号。

## 在 npm CLI 中管理访问令牌

> [!WARNING]
> 使用 `npm CLI` 无法创建“自动化令牌”以及“细粒度令牌”，它们只能使用 npm 网站创建。

```shell
# 创建写入令牌
npm token create 

# 创建只读令牌
npm token create --ready-only

# 查看令牌明细
npm token list --json

# 移除指定的令牌
npm token revoke <tokenID>
```

## 令牌的生成与销毁

**以下行为会生成令牌：**

- 在终端中使用 `npm login` 登录用户会生成访问令牌。
- 在 NPM 官网 - `Access Tokens` 页面可以创建经典令牌、自动化令牌以及细粒度令牌。
- 在终端中可以使用 `npm token` 命令创建只读令牌、写入令牌以及带有 CIDR 的令牌。

**以下行为会销毁令牌：**

- 在终端中使用 `npm token revoke` 命令可以销毁令牌。
- 在终端中使用 `npm logout` 命令退出用户可以自动销毁对应用户身份令牌。
- 在 NPM 官网 - `Access Token` 页面可以移除令牌。

对于配置在 `.npmrc` 文件中的访问令牌，使用 `npm logout` 命令也会使其被销毁。
```ini
registry=https://registry.npmjs.org/
//registry.npmjs.org:_authToken=xxxxx
```