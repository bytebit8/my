NPM (Node Package Manager) Node 的包管理器。
NPM 由三部分组成：

- **网站 [npmjs.com](http://npmjs.com)**
- **注册表(Registry**) [registry.npmjs.org](https://www.registry.npmjs.org/)
- **命令行工具(CLI)**。

### 网站 - [npmjs.com](http://npmjs.com)

npm 站点最常用的功能就是检索包和查看包的详情。

![[Pasted image 20240907102227.png]]

- **①** 严格匹配的“包”。
- **②** PQM：人气（**Popularity**）、质量（**Quality**）、维护情况（**Maintenance**）。
- **③** 按照 OPQM 对搜索结果进行排序。其中（**Optimal**）为最佳。

点击包名，可以进入包的详情页：
![[Pasted image 20240907103253.png]]
- **①** `Typescript Icon` 表明包有内置 Typescript 类型文件。
- **②** 包的自述文件。
- **③** 包的源代码。
- **④ ⑤** 包的依赖信息与被依赖信息。
- **⑥** 包的版本

还有另一种 `DefinitelyTyped` 的 ICON，表示该包存在单独的类型声明包。开发者可以选择单独安装，这些包通常都是以 `@types/xxx` 为命名格式。

在详情页的右侧，可以查看有关“包”的更为详细信息：
- 仓库地址
- 包的主页
- 周下载量
- 协议
- 包的体积大小
- 发布时间
- ...

对于自己名下拥有的包，还有一个 `Settings` 项，可以设置包的可访问性、邀请维护者、废弃以及删除包。

除了以上提供的核心基础功能外，NPM 站点还提供了两个较为高级的功能，分别是“访问令牌（Access Token）”和“组织（Organizations）”。

**访问令牌(Access Token)：**
![[Pasted image 20240912230636.png]]
NPM 站点提供了两种令牌的创建，分别是：
- Classic Token（经典的 Access Token，也是 NPM 支持的最早期的访问令牌），它拥有对用户账户完全的访问权限。
- Granular Access Token（细粒度的访问令牌，NPM 支持的最新的访问令牌）可以创建具有特定权限的令牌，例如只能读取包、发布包，让用户身份认证更加安全。

>[!tip]
>在持续集成与持续部署 CI/CD 中最常用到的还是 `Classic Access Token`。 

**组织(Organizations)**

“组织”是 NPM 官方提供的协作方式。
![[Pasted image 20240912231529.png]]
“组织”包含了”包“、”成员“与”团队“。最为紧密的则是”团队“，组织下的”包“、”成员“都可以被添加到指定的团队中进行管理。

如果想在 NPM 中发布与安装私有的包“Private Packages”，可以在账单信息页面中变更计划（注意：需要付费）。
![[Pasted image 20240912231921.png]]
> 更多有关组织的内容，请访问：
> [NPM | 组织机构](https://www.notion.so/NPM-9b2ec2c74a2d48c49c8ee1c32de16964?pvs=21)

### 注册表（Registry）

NPM 注册表是一个大型的公共数据库（由 CouchDB 数据库提供支持），包含了所有包的元信息。

注册表的具体地址由“包”的可访问性决定，对于 NPM 公共注册表其地址如下：
```text
https://registry.npmjs.org/
```

如果要查看某个包的所有元信息，便可以通过注册表+包名来检索：
```bash
curl https://registry.npmjs.org/npm
```

### 命令行工具 (CLI)

包管理器提供的终端工具（客户端），也是开发者与 NPM 最常用的交互方式。
npm CLI 本身也是一个 nodeJS 包：[https://www.npmjs.com/package/npm](https://www.npmjs.com/package/npm)
