## 简介

`Browserslit` 是一款共享 JavaScript 运行时（浏览器和 Node.js）兼容性的版本配置工具。它可以为不同的前端工具共享所需兼容的目标浏览器或 Node.js 版本。

哪些前端工具在使用？

- **Webpack**
- **babel-preset-env**
- **eslint-plugin-compat**
- **autoprefixer**
- **post-preset-env**
- **@vue/cli-service**
- **@vitejs/plugin-legacy**

## 作用

`Browserslist` 可以在浏览器兼容性和应用包体大小之间保持合适的平衡。而“兼容性”则影响了应用可以覆盖的用户范围。

**为什么没有 Node.js？**

因为 Node 作为服务端，可以由服务提供者自行决定，从根本上而言，没有兼容性问题。

## 安装

```bash
npm i browserslist -D
```


## 原理

`browserslist` 会读取配置的查询条件，查找当前工程所需兼容的目标浏览器版本范围。其底层实现是对 `caniuse-lite` 的查询进行了封装。

[caniuse-lite](https://github.com/browserslist/caniuse-lite) 是 [caniuse-db](https://github.com/Fyrd/caniuse) 的较小型版本，它删减掉一些服务端的字段，只返回客户端(浏览器)层面所需的关键数据。

总的来说 `browserslist` 会将查询的条件传递给 `caniuse-lite` 工具进行查询以返回符合条件的浏览器或 Node 版本列表（返回的版本列表本质是开发人员所期望兼容的版本）。其它工具，例如 `babel-preset-env` 会将这些要兼容的版本列表和自己内部存储的 JavaScript 语法特性兼容性数据进行比较，确定哪些语法或特性不在这个版本范围列表中，从而有选择的打入相应的 Polyfill 库。

>`caniuse-db` 是 `caniuse.com` 的底层数据库

## 配置

为`browserslist`提供查询条件的方式主要有两种：

### package.json

```json
{
  "browserslist": [
    "> 1%",
    "ie 11", 
    "not dead"
  ]
}
```

区分开发与生产环境：
```json
"browserslist": {
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ],
    "production": [
      "> 1%",
      "ie 11",
      "not dead"
    ]
  }
```

### .browserslistrc

> 注释以 `#` 号开头

```
> 1%
ie 11
not dead
```

区分开发与生产环境：
```
[development]
last 1 chrome version
last 1 firefox version
last 1 safari version

[production]
>= 0.5%, last 2 versions, Firefox ESR, not dead
```

>[!NOTE]
>`browserslist` 默认从当前目录查找，如果没有再依次向上级目录查找，如果不存在配置文件，则会使用**默认配置**。

`browserslists` 默认为 `production` 环境，可以通过 `NODE_ENV` 环境进行自定义。

## 默认配置

如果没有提供配置文件，`browserslist` 的默认的查询条件为：
```
>0.5%, last 2 versions, Firefox ESR, not dead.
```

* `>0.5` ：表示市场占有率达到 `0.5%` 以上的浏览器版本。
* `last 2 versions` ：浏览器最新的 2 个版本。如果只是占有率 `0.5%` 以上，那么可能导致各个浏览器最新的版本可能会没有覆盖到。
* `Firefox ESR` ：Firefox 扩展支持版(Extended Support Release)。与普通版本相比它的更新频率由 4 周降低为 42 周，因此也更加的稳定。
* `not dead` ：还在更新维护的浏览器版本。24 个月内没有获得过官方更新和维护的浏览器版本，单纯 `>0.5` 还是可能会引入一些不在支持的浏览器版本，例如 `ie8, ie11` 等。 

## 查询语法

### 常用条件

- 默认配置：
	- `defaults`
- 按使用情况
	- `> 0.5%` | `>=0.3%`
	- 带有特定区域：`>0.5% in CN` | [所有区域代码](https://github.com/browserslist/caniuse-lite/tree/main/data/regions)
* 覆盖范围内的流行的最新浏览器
	* `cover 99.5%`
	* 带有特定区域：`cover 99.5% in US
- 浏览器版本：
	- 固定版本： `ie 10`
	- 版本范围：`ie 6-8`
	- `chrome > 20`
	- `Firefox ESR`
- 最新版本：
	- `last 2 versions` 
	- 特定浏览器：`last 2 Chrome versions`
	- 特定版本类型： `last 2 major versions` | `last 2 iOS major versions`
- 不在支持的浏览器（24个月内未受官方更新与维护的浏览器）：
	- `dead`
	- 取反：`not dead`
- 浏览器特性支持（所有的 [features](https://github.com/browserslist/caniuse-lite/tree/main/data/features)）：
	- 完全支持：`fully supports es6-module`
	- 部分支持：`supports css-grid` | `partially supports css-grid`
 - 按时间范围：
	 - `since 2015`
	 - `last 2 years`
- 扩展配置：
	- `extends browserslist-config-example`
- Node.js:
	- 固定版本：`node 18` | `node 18.2`
	- 维护版本： `maintained node versions`
	- `last 2 node versions`
	- `last 2 node major versions`

### 组合条件

合并多个条件。

- `not` "非运算"。 对条件取反。
- `and` “与运算”。 对多个条件取交集。
- `or` “或运算”。对多个条件取并集，可以用简写 `,` 替代。

示意图：
![[前端工程化/Browserslist/assets/8b396b52deea9350ff7be3a7b5a63b00_MD5.jpeg]]

范例：
```
last 2 versions or chrome > 70 # last 2 versions, chrome > 70
last 2 versions or chrome >= 70 and > 0.5%
```

#### not

你可以使用 `not` 在任何查询条件之前进行取反。但是不能以 `not` 作为起始的条件，因 `browserslist` API 实现的特殊性，`not` 关键字左侧必须要有一个查询与之进行组合。
```
defaults, not dead
ie > 0, not ie 8
ie > 0 AND not fully suports es6-module
```

#### and

取查询条件结果的交集。因 `browserslist` API 实现的特殊性，`and` 关键字左侧必须要有一个查询与之进行组合。
```
defaults and partially supports css-grid 
```

#### or

取查询条件结果的并集。可以使用逗号 `,` 简写。 
因 `browserslist` API 实现的特殊性，`or` 关键字左侧必须要有一个查询与之进行组合。
```
>0.5%, Firefox ESR, not dead, last 2 versions
```


### 更多查询条件

> 查询条件的更多使用方式，可以查阅官方文档：[https://github.com/browserslist/browserslist#full-list](https://github.com/browserslist/browserslist#full-list)


## 自定义数据

这对于需要使用 `browserslist` 查询私有站点的统计数据非常有用。

**先决条件：**

统计数据的格式必须要满足 `browserslist` 的要求。
下面是统计数据的 JSON 格式：
```json
{
  "ie": {
    "6": 0.01,
    "7": 0.4,
    "8": 1.5
  },
  "chrome": {
    …
  },
  …
}
```

**与 Google Ga 集成**
- `npx browserslist-ga` 在线访问 Google Analytics（需要提供账号/密码），直接生成 `browserslist-stats.json`。
- `browserslist-ga-export` : 转化本地的 GA 统计数据。

>[!NOTE]
>请注意，您可以查询自定义使用情况数据，同时还可以查询全球或区域数据。例如，查询 `> 1% in my stats, > 5% in US, 10%`是允许的。

## 扩展配置

可以将你的查询条件打成一个 npm 包，以在不同的项目中共享配置。

> 从其它 npm 包中获取查询条件

1. 包的根路径中存在一个 `index.js`
2. `index.js` 导出一个数组。

```jsx
module.exports = [
	"> 0.5%",
	"not dead",
	"Firefox ESR",
	"last 2 versions"
]
```

包名的前缀请固定以 `browserslist-config-*` 为前缀，例如你的包名叫做 `browserslist-config-example` ，在使用时，你首先需要安装：
```bash
npm i browserslist-config-example -D
```

然后通过 `extends` 进行使用。
```json
{
  "browserslist":[
    "extends browserslist-config-example"
  ]
}
```

## 在线调试

### npx browserslist

为了更方便的验证查询条件，你可以通过 `npx` 命令在本地创建一个临时的环境用于执行 查询语句。

```shell
npx browserslist "> 0.5%, not ie 11"
npx browserslist "chrome > 70 and > 1%"
npx browserslist "extends **browserslist-config-example**"
```

### browserslist.dev

也可以直接通过在线网站输入查询条件进行验证：[Browserslist Dev](https://www.browserslist.dev/?q=bGFzdCAyIHZlcnNpb25z)

## JS API

API 使用格式：

```jsx
const browserslist = require('browserslist')

// Your CSS/JS build tool code
function process (source, opts) {
  const browsers = browserslist(opts.overrideBrowserslist, {
    stats: opts.stats,
    path:  opts.file,
    env:   opts.env
  })
  // Your code to add features for selected browsers
}
```

查询可以是字符串`"> 1%, not dead"` 或数组`['> 1%', 'not dead']`。

如果缺少查询，Browserlist 将查找配置文件。您可以提供一个`path`选项（可以是文件）来查找与之相关的配置文件。

- `path`：查找配置文件的文件或目录路径。默认为`.`。
- `env`：配置中使用哪个环境部分。默认为`production`。
- `stats`：自定义使用统计数据。
- `config`：如果您想手动设置，则配置路径。
- `ignoreUnknownVersions`：忽略未知版本，例如 ie 12。默认值为`false`。
- `dangerousExtend`：禁用`extend`查询的安全检查。默认为`false`。
- `throwOnMissing`：如果找不到环境，则抛出错误。默认值为`false`。
- `mobileToDesktop`：如果 `caniuse-db` 没有关于此移动版本的数据，则使用桌面浏览器。 `caniuse-db` 仅包含有关最新版本的移动浏览器的数据。默认值为`false`。

### coverage

获取符合条件的浏览器版本所占的覆盖率。

```jsx
import browserslist from 'browserslist';

browserslist.coverage(browserslist('> 1%'));
browserslist.coverage(browserslist('> 1% in US'), 'US');
```

### 清除缓存

`browserslist` 会缓存 `package.json` 或 `.browserslistrc` 的配置，你可以使用 `clearCaches()` 方法来清除这些缓存。

```jsx
import browserslist from 'browserslist';

browserslist.clearCaches();
```

## 环境变量
| 变量名称                  | 说明                                                      |
| --------------------- | ------------------------------------------------------- |
| `BROWSERSLIST`        | BROWSERSLIST="> 5%" npx webpack                         |
| `BROWSERSLIST_CONFIG` | BROWSERSLIST_CONFIG=./config/browserslist npx webpack   |
| `BROWSERSLIST_ENV`    | BROWSERSLIST_ENV="development" npx webpack              |
| `BROWSERSLIST_STATS`  | BROWSERSLIST_STATS=./config/usage_data.json npx webpack |

## 常见问题
### caniuse-lite is outdated

```bash
Browserslist: caniuse-lite is outdated. Please run:
npx browserslist@latest --update-db
Why you should do it regularly: <https://github.com/browserslist/browserslist#browsers-data-updating>
```