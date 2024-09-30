## 什么是 npm 包？

```
A package is a file or directory that is described by a package.json file. 
```

**“包”** 是用 `package.json` 文件描述的文件或包目录。 npm 包必须要包含 `package.json` 文件。

> “包”的形式有多种。可以是含有 `package.json` 的文件夹、压缩包或者是指向它们的 URL 地址。

## 什么是模块？

一个文件就是一个模块，“包” 包含 “模块”。在 NPM 中“模块”指的是 `node_modules` 目录内可以被 Node.js 加载的文件。

在以下的 Node.js 程序上下文中：

```jsx
const request = require('axios');
```

我们通常会说 `request` 是从包 `axios` 的某个模块中导入的成员。使用包名加载的模块，通常是这个包的入口文件（入口模块），由 `package.json` 文件的 `main` 字段指定。

>[!tip]
>模块的解析与加载规则由具体的构建工具所决定，例如 `Webpack`、`Rollup`、`Vite`。

## 包的命名格式

```
[@scope/]<name>@<version | tag>
```
- `scope` : 包的作用域名称，是完整包名的组成部分。
- `name`  : 包的具体名称。
- `version` : 包的版本号。
- `tag` ： 包的分发标签。

## 包的版本号

npm 包的版本号（version）遵循语义化的版本控制。其格式为 `x.y.z` 形式，`x.y.z` 只能为数值 0-9，且不允许前导补零，例如：`1.0.0`。

- `x` (**major**): 主版本号，表示不兼容的破坏性功能更新（BREAK CHNANGE）。
- `y` (**minor**): 次版本号，兼容性的功能更新。
- `z` (**patch**): 修订版本号（补丁版本号），兼容性更新，只是修复问题。

当包的功能还处于快速开发阶段，并且提供的 API 还不稳定时，包的主版本号可以从 `0` 开始，比如： `0.1.1`。当软件包提供的 API 趋于稳定后，便可以发布第一个稳定版：`1.0.0`。

与稳定版对应的还有一个“预发布版”，在格式上如下所示：
```text
 x.y.z-[pre]
```

预发布版(pre)，在命名上只能由数值字母与点号组成`[a-zA-Z.0-9]`，除此之外，不能有其它字符或符号：
```text
1.0.0-beta.1
1.0.0-rc
1.0.0-0.3.7
```

预发布版本主要用于标记软件包的不同阶段状态，从而更有效的让发布者分发他们的包。

>[!note]
>更详细的内容可以阅读：[[语义化版本控制和分发标签]]

## 包的分发标签

分发标签（dist-tag）相当于版本号的别名，使包的版本号更具有可读性。

NPM 默认的分发标签只有一个，那就是 `@latest` 用于指向包的最新版本。
分发标签与版本号的对应关系是一对一的关系，同一时刻一个分发标签只能关联一个版本号。

分发标签还与包的预发布版存在着紧密的联系，它使得用户安装软件包不同阶段的版本更加的方便。

>[!note]
>更详细的内容可以阅读：[[语义化版本控制和分发标签]]

## 包的注册表

注册表本质是一个 npm 包的数据库服务。在安装包时，会从数据库中下载包并解压到本地；在发布包时，会在本地打 tarball，并提交数据库。

npm 包默认的公共注册表地址是：
```text
https://registry.npmjs.org/
```

也可以通过 `npm config` 命令在终端中指定：
```shell
npm config set registry https://registry.npmjs.org
```

或者在 `.npmrc` 配置文件中配置：
```text
registry=https://registry.npmjs.org/
```
## 包的作用域

包的“作用域(scope)” 就是包的命名空间，它限定了包的范围和组织方式。

### 作用域的命名规则

作用域名是包名称的可选组成部分。包的拥有者可以使用 NPM 的“账户名称”或“组织名称”来作为包的作用域名称，作用域名以`@`开始 ，以`/`结尾。
```text
@scope-name/package-name
```

由于包名会作为 URL 地址的一部分出现，因此组成包名的字符必须是安全的 URL 字符(`URL-safe characters`) [[URI：Uniform Resource Identifier]]

>同一命名空间下包名不允许重复，但在不同命名空间下，可以创建相同名称的包。

### 作用域包的范围

包的作用域也影响着包的可访问性：
- 无作用域的包总是被公开的。
- 作用域包默认私有，也可以在发布时设置为公开。

### 作用域包的组织方式

针对作用域包，NPM 会用作用域名称在 `node_modules` 目录下新建一个作用域目录，用于存储同一作用域下的所有包。
```text
@vue
├── babel-helper-vue-jsx-merge-props
├── babel-preset-app
├── eslint-config-prettier
├── eslint-config-typescript
├── cli-plugin-babel
├── cli-plugin-eslint
├── test-utils
└── vue2-jest
```

### 作用域包的注册表

由于作用域包默认的访问范围为“私有(Private)”，所以通常需要为作用域包单独设置单独的注册表地址。
在 `.npmrc` 文件中，可以通过 `[@your-scope-name]:registry` 的自定义字段来为作用域设置私有的注册表。
```text
# 作用域对应的私有注册表地址。
@scope-name:registry=https://your-scope.registry.npmjs.org/

# 公共注册表地址。
registry=https://registry.npmjs.org/
```

或者，你也可以使用 `npm config` 命令在终端中设置：
```shell
npm config set @scope-name:registry https://xxx.registry.npmjs.org
```

当发布或安装作用域包时，npm CLI 会优先通过包的作用域名称来匹配对应的注册表地址，如果没有查找到作用域对应的注册表地址，便会默认使用公共注册表地址。

也正是因为这个特性，我们可以很好的将 npm 与第三方注册表服务进行集成，例如:[Verdaccio](https://verdaccio.org/)

作用域与注册表具有一对多的关系。一个注册表可以托管多个作用域包，但一个作用域包同一时刻只能指向一个注册表地址。

