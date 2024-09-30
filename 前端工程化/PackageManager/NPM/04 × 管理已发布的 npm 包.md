>[!note]
>以下所有操作，都要求用户是“包”的拥有者或维护者。

## 更新包的版本号

按照语义化版本控制要求，变更“包”的任何内容都需要提升包的版本号并重新发布“包”。版本号的变更推荐使用 npm CLI 提供的 `version` 命令，不要手动变更包的版本号。
```shell
npm version patch # 提升补丁版本号
npm version minor # 提升次版本号
npm version major # 提升主版本号
```

如果“包”的项目本身启用了版本控制，执行上述命令会同时创建一个 `tag` 和 `commit`。此时使用 `-m` 参数可以自定义 Git Commit 的 Message:
```shell
npm version patch -m "Version [%s] released on $(date '+%Y-%m-%d %H:%M:%S')"
#    Version [1.0.1] released on 2024-09-17 20:45:43
```

## 更改包的可访问性

切换“包”范围是“私有”还是“公开”。
需要注意：该功能需要是付费账户。

**使用 NPM 站点：**
1. 进入“包”的详情页
2. 点击 “Settings” 选项。
3. 在 "Package Access" 栏目下选择“select "Is Package Private/Public?"。

**使用 npm CLI 方式：**
```shell
npm access public <package-name>     # 公开包
npm access restricted <package-name> # 私有包
```

>[!note]
>1. “私有包”一定是作用域包，只有“作用域包”才能够设置范围为私有。
>2. 作用域包也可以设置为公开。
>3. 作用域名可以是用户名，也可以是组织名。
>4. 包的私有服务必须是付费账户。

## 添加协作账户

为包添加 “Maintainer” 账户。

**NPM 站点方式：**
1. 进入“包”的详情页
2. 点击 “Settings” 选项。
3. 在 “invite maintainer” 栏目下输入 UserName，并点击 “Invite” 按钮。

**npm CLI 方式：**
```shell
npm owner add <username> <package-name>
```

添加完成后，系统会向被邀请的用户发送一封邮件，被邀请用户只需要访问要求连接，点击 accept 即可成为“包”的拥有者。


## 转移包到另一个账户

当账户成为协作账户后便会拥有“包”的 `write` 权限。协作账户可以通过移除”包“的其它拥有者来实现包的转移。

```shell
npm owner rm <username> <package-name>
```

如果开启了双因素认证，还需要输入一次性密码：
```shell
npm owner rm <username> <package-name> --opt=xxxx
```

>[!warning]
>作用域包不能转移到另一个账户，因为作用域名称就是当前拥有者的用户名或组织名称，作用域包只能在旧的账户下删除然后在新的账户下重新发布。

## 废弃包

废弃包不会从注册表中删除，而是会在安装时输出一条警告信息。
```shell
➜ npm i xxxx-example-package npm WARN deprecated xxxx-example-package@1.1.0: Package no longer supported. Contact Support at https://www.npmjs.com/support for more info.
```

**NPM 站点方式：**
1. 进入“包”的详情页
2. 点击 “Settings” 选项。
3. 在 “Deprecate package” 栏目下点击 “Deprecate package” 按钮。
4. 输入确认包名确认废弃。

**npm CLI 方式：**
```shell
npm deprecate <package-name> "This a package is deprecated."
```

使用命令的方式，还能单独废弃包的指定版本：
```shell
npm deprecate <package-name@version> "This version of package-name is deprecated."
```

取消废弃只需要删除废弃信息即可：
```shell
npm deprecate <package-name> ""
```

## 删除包

“删除包”是真的从注册表中移除包。若要删除包，则必须要满足以下条件：
1. “包”发布没有超过 72 小时。
2. 超过 72 小时则需要满足下面条件：
	1. 近一周下载量没有超过 300
	2. 没有依赖的“包”。

**NPM 站点方式：**
1. 进入“包”的详情页
2. 点击 “Settings” 选项。
3. 在“Delete package” 栏目下点击 “Delete package” 按钮
4. 输入“包”名确认删除包。

**npm CLI 方式：**
```shell
npm unpublish <package-name> -f
```

或者删除指定的版本：
```shell
npm unpublish <package-name@version> -f
```

>“包” 被删除后 24 小时内不能使用相同的名称进行再次发布。
