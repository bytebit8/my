## package.json 是什么？

`package.json` 是一个 JSON 格式的 “包” 描述文件，用于定义 npm 包。存放于包项目的根目录。

`package.json` 文件记录了有关包（项目）的元数据、配置以及依赖等信息。

## 包的元数据

### name

包名。用于唯一的标识该包，可以通过包名在 NPM 站点中搜索包。

组成包名的字符集必须是安全的非保留的 URI 字符(`URL-safe characters`)。这是因为包名会作为 URL 的一部分出现。

- 数字 `0-9`
- 字母 `a-z`
- 波浪号：`~`
- 下划线：`_`
- 连字符：`-`
- 点号: `.`

> 不要使用大写字母作为“包名”。

包名的格式有两种：“作用域包”和“无作用域包”。

无作用域包：
```json
{
  "name":"package-name",
  "version":"1.0.0"
}
```

作用域包：
```json
{
  "name":"@scope-name/package-name",
  "version":"1.0.0"
}
```

### version

包的版本号，遵循语义化版本控制 [[语义化版本控制和分发标签]]。
包的名称与版本号构成了对包某一版本的唯一标识。如果需要发布包，那么它们是必须的。

> npm CLI 底层依赖 [`node-semver`](https://github.com/npm/node-semver) 对 `version` 字段进行解析。

### private

私有包。设置了 `private` 为 `true` 那么 npm 将会阻止发布该包。

> 这对 `MonoRepo` 工作区中的本地包很有用。

### description

包的一段描述文案，可用于被 `npm search` 搜索。

### keywords

包的关键字，它是一串字符串数组。可用于被 `npm search` 搜索。
```json
{
  "name":"package-name",
  "version":"1.0.0",
  "keywords":["key1", "key2"]
}
```

### homepage

包的主页 URL。可以是 GitHub 或 GitLab 中仓库的首页。
使用 `npm docs` 命令可以打开地址。

### author

包的作者信息。
```json
{
  "name" : "Barney Rubble",
  "email" : "b@rubble.com",
  "url" : "<http://barnyrubble.tumblr.com/>"
}
```

### contributors

包的贡献者。
```json
{
  "contributors":[
    {
      "name" : "Jack",
      "email" : "b@jack.com",
      "url" : "<http://jack.tumblr.com/>"
    },
    {
      "name" : "Jan",
      "email" : "b@jan.com",
      "url" : "<http://jan.tumblr.com/>"
     }
  ]
}
```

### \*maintainers

该字段是隐含的，npm 会自动使用包的 owner 信息来生成 `maintainers` 字段。
```shell
➜  ~ npm view vue maintainers
[
  'soda <npm@haoqun.me>',
  'yyx990803 <yyx990803@gmail.com>',
  'posva <posva13@gmail.com>'
]
```

### bugs

项目问题的 url 或着反馈问题的邮件地址。
使用 `npm bugs` 命令可以打开地址。
```json
{
	"bugs":{
		"url":"<https://github.com/your-project/issues>",
		"email":"xxx@email.com"
	}
}
```

### repository

包源代码的托管位置。
使用 `npm repo` 命令可以打开仓库地址。
```json
{
	"repository":{
		"type":"git",
		"url":"<https://github.com/you-project/>"
	}
}
```

### licence

包的许可证。
如果您不希望在任何条款下授予他人使用私有或未发布包的权利：
```json
{
  "license": "UNLICENSED"
}
```

常见的许可证有：`ISC` 、`MIT` 等。

### engines

指定 “包” 适用的 node 或 npm 版本。
```json
{
	"engines":{
		"node":">=16 <=18",
		"npm":"~9.0.0"
	}
}
```

> 只有用户开启了 `engine-strict` 配置，该限制才会有效，否则只会作为建议信息在安装您的软件包时输出警告。

### cpu

指定“包”适用于的 cpu 架构。
常见的架构类型有：`x64` 、`ia32`、 `arm` 、`mips` 等。
也可以使用取反运算符：`!ia32`
```json
{
	"cpu":["x64", "ia32"]
}
```

### os

指定“包”适用于的系统。
常见的系统类型有：`darwin` 、`linux`、`win32`。
也支持取反运算符：`!darwin` 。
```json
{
	"os":["!win32"]
}
```

## 包的入口点

### 历史概述

包的入口点就是包内所有模块的入口模块。模块加载器会加载 `./node_modules` 目录中的包，并顺着包对外暴漏的入口文件来生成整个项目 Depency Graph 的一部分。

npm 在 Commonjs 时代为 `package.json` 定义了两个标准字段 `main` 与 `browser` 。其目的是为不同的运行时提供不同的入口点，但当时这些入口模块都是 Commonjs 标准的模块，所以也让 `main` 和 `browser` 字段和 Commonjs 格式建立了隐形上的对等关系。随着 ESMoule 标准的提出，构建工具为了能够更好的兼容之前的包，便扩展了一个 `module` 字段，专门用于为包定义 `ESM` 格式的入口点。

在 Node.js 社区对 ESMoule 标准支持的完善过程中，又对 `package.json` 扩展了 `type` 字段，用于声明“包”中模块采用的何种模块标准。当 `type:module` 说明包中所有模块采用的是 ESModule 标准；缺省该字段或为 `type:commonjs` 则表明包采用的是早期的 Commonjs 标准。引入该字段的好处是可以替代 `module` 字段，让之前的 `main` 、 `browser` 字段和隐含的模块格式解耦，而代价就是无法为包分发多种模块格式。

Google 的 V8 引擎还推荐了另一种方案（无需为 package.json 文件扩展新的字段），通过为 `.js` 引入新的扩展名来关联对应的模块格式，例如 `.mjs` 代表了 ESMoule 标准； `.cjs` 代表了 Commonjs 标准。Node.js 与 构建工具都对此进行了支持，当为 JavaScript 模块指定了新的扩展名后，将会自动忽略包的 `type` 字段，总是按照扩展名对应的模块标准来解析模块。

同时，JavaScript 的另一条线路 —— TypeScript ，也对 `package.json` 进行了扩展，增加了一个 `types` 字段，用于定义包的类型文件的入口。

最终到了 `Node.js V12.20.0` 版本，通过使用 `--experimental-conditional-exports` 标志来开启实验性质的 `exports` 字段，才真正开始完善了包的入口点配置。 `exports` 不仅可以配置包的入口模块导出，还支持包的子路径导出(subpath exports)、条件导出(conditional exports)和嵌套的条件导出，提高了包的封装性，屏蔽了包中未导出的模块。
### main

包的入口文件。Node.js 或模块构建工具会读取该字段从入口模块开始加载包中的其它被依赖的模块，从而构建出整个项目依赖图的一部分。
```json
{
	"main":"./src/main.js"
}
```

- **如果包未设置 `main` 字段，则默认使用“包”根目录下的 `index.js` 文件。**
- `main` 字段指定的入口文件可以应用在所有运行时环境（Node.js 环境 & 浏览器环境）。

### browser

指定包在**浏览器运行时**下的入口文件。

该字段属于社区沉淀字段，主要被前端的模块构建工具所识别，当包需要基于不同的运行时环境提供不同的入口文件时，该字段将非常有用。

```json
{
	"main":"./pkg.node.js",
	"browser":"./pkg.esm.browser.js"
}
```

当模块打包器读取以上包的入口点配置时，如果构建目标是 `web` 则优先读取 `browser` 字段；若构建目标为 `node` 则读取 `main` ；而在 Node.js 环境中总会读取 `main` 。

> Webpack 的 `target` 字段为 `web` 时，入口点字段依次会取：`[browser, module, main]`

若限定包只能用作浏览器环境，则用 `browser` 字段替换 `main` 字段即可：

```json
{
	"browser":"./pkg.esm.browser.js"
}
```

>[!NOTE]
>`main` 与 `browser` 字段的早期设计目的只是为了区分运行时的不同。

## 包的 bin 程序

### bin

定义“包”对外提供的可执行命令（可执行程序）。
```json
{
  "name":"bin-example-package",
  "bin":"./bin/index.js"
}
```

在使用 `npm i` 安装包时，如果安装的是全局依赖，则创建可执行文件的符号链接到 `${prefix}/bin` 目录中；对于非全局包 ，则创建可执行文件的符号链接到项目的 `./node_modules/.bin` 目录中。

通过 Node.js 安装程序、NVM 或者是系统的软件包管理器（homebrew，apt）等，会自动将 Node 程序的安装目录 `${prefix}/bin` 路径加入到系统的 `PATH` 变量中，所以全局包的 `bin` 命令，可以直接在终端中执行：
```shell
npm i npm-check-updates -g;

npm view npm-check-updates bin
# { 'npm-check-updates': 'build/cli.js', ncu: 'build/cli.js' }

ls -lh $(npm config get prefix)/bin;
# lrwxr-xr-x  1 xxx  staff    58B  5  7 11:10 ncu -> ../lib/node_modules/npm-check-updates/build/src/bin/cli.js

which ncu;
# /Users/xxx/.nvm/versions/node/v18.18.2/bin/ncu

echo $PATH; 
# /Users/xxx/.nvm/versions/node/v18.18.2/bin

ncu -v # 16.14.20
```

对于安装的非全局的本地依赖，可以在执行 npm 脚本命令时调用包的 `bin` 程序 [[13 × 脚本 (Scripts)]] ，这得益 npm 脚本在执行时会在自动将项目的 `./node_modules/.bin` 路径加入到当前终端的临时 `PATH ` 变量中：
```shell
npm i bin-example-package;
npm pkg set scripts.test="bin-example-package"

npm test;
```

除了上述两种方式执行包的 `bin` 程序外，还可以使用 `npm exec` 命令直接调用 npm 包的指定 bin 程序。
```shell
npm exec typescript tsc -v
npm exec eslint .
npm exec figlet "Hello world\!"
npm exec --package figlet --package chalk-cli -c "figlet 'Hello' | chalk red bold --stdin";
```

默认情况下，`bin` 程序的命令名称就是包名称，如果要自定义命令名称或同时定义多个命令，可以传入一个对象：
```json
{
	"bin":{
		"exec-a":"./bin/exec-a.js",
		"exec-b:":"./bin/exec-b",
		"exec-c":"./bin/exec-c.mjs"
	}
}
```

如果以上配置较为繁琐，也可以使用 `directories.bin` 字段。

### **directories.bin**

将指定目录下的所有可执行文件都作为包对外提供连接的命令。
这大大简化了在包中进行多个 bin 文件的配置。

```json
{
  "directories":{
    "bin":"./bins"
  }
}
```

>注意：该字段不能与 `bin` 字段同时使用。

## 包的脚本与配置

### scripts

前置知识：[[13 × 脚本 (Scripts)]]

设置包的脚本命令。值是一个对象，属性是脚本命令名称，值则是脚本命令对应的可执行程序。

作为对比，`bin` 程序是包对外提供的可执行命令，脚本则是包对内提供的可执行程序。
  
包的脚本可以分为“内置脚本”与“自定义脚本”。有的内置脚本具有生命周期脚本，会在脚本命令执行时触发一系列与之有关的脚本命令。不论是内置脚本命令还是用户自定义脚本命令，都支持命令的前置（`pre`）与后置（`post`）。

```json
{
	"scripts": {
		"start": "node ./server.js",
		"build:webpack": "webpack --config ./build/webpack.dev.config.js",
		"build:rollup": "rollup -c"
	}
}
```

### config

为包的脚本命令设置自定义参数。值是一个对象，会在脚本命令执行时，解析为`npm_package_config_*` 固定前缀格式的环境变量：
```json
{
	"scripts": {
		"start": "echo $npm_package_config_port"
	},
	"config": {
		"port": 3000
	}
}
```

## 依赖管理

### dependencies

**定义包的生产依赖。**

这些依赖的产物会打包到运行时代码中。

> 如果有人要依赖您的包，在安装您的包时，这些依赖也会一同被安装。

### devDependencies

**定义包的开发依赖。**

这些依赖的产物不会被打包到运行时代码，只用于包的开发环境。

> 简而言之，如果有人要依赖您的包，`devDependencies` 中的依赖是他无需安装也不会影响项目的正常运行。

### peerDependencies

**定义包的对等依赖。**

该字段反过来要求宿主环境中的依赖信息要符合 `peerDependencies` 字段声明的条件。只有宿主包依赖情况符合条件，才能安装您的包。这很好的保证了依赖关系的兼容性，因此常用于为库或框架开发插件时使用。

```json
{
  "name": "react-custom-plugin",
  "version": "1.0.0",
  "peerDependencies": {
    "react": "^18.0.0"
  }
}
```

给定一个宿主包，其包信息如下：

```json
{
	"name": "rc-demo",
	"dependencies": {
		"react":"^18.3.0"
	}
}
```

那么，此时执行 `npm i react-custom-plugin` 后，可得到以下依赖树：

```
rc-demo
├── react@18.3.1
└── react-custom-plugin@1.0.0
```

> 出于提升兼容范围的考虑，请确保您的插件要求的宿主版本尽可能的广泛，而不是将其锁定到特定的补丁版本。

npm 6.x 及以下版本在遇到无效的对等依赖时，会输出警告信息并阻止依赖安装；到了 npm 7.x 版本则默认允许安装。你可以使用 `strict-peer-denpendencies` 配置选项进行自定义控制。

### peerDependenciesMeta

**定义对等依赖元信息。**

如果宿主环境中安装的依赖不符合 `peerDependencies` 字段指定的条件，那么 npm 将会发出警告。在 `peerDependenciesMeta` 中，你可以将指定的对等依赖条件标记为可选，这样可以确保 npm 不会发出警告。

```json
{
	"peerDependenciesMeta": {
		 "soy-milk": {
		   "optional": true
		 }
	}
}
```

### optionalDependencies

**定义包的可选依赖。**

正如其名，包的代码在运行时就算找不到所需的依赖也不会影响继续执行。当然，处理依赖不存在的情况依然是在您的逻辑代码中进行处理：

```json
try {
  var foo = require('foo')
  var fooVersion = require('foo/package.json').version
} catch (er) {
  foo = null
}
if ( notGoodFooVersion(fooVersion) ) {
  foo = null
}

// .. then later in your program ..

if (foo) {
  foo.doFooThings()
}
```

可以使用 `npm install foo --omit optional` 来帮助测试。

`optionalDependencies` 会覆盖 `dependencies` 中同名的依赖，因此通常最好只定义在一个位置。

```json
{
	"optionalDenpendencies": {
		"foo":"^1.0.0"
	}
}
```

### bundleDependences

**定义包的捆绑包。**

连同依赖一同打 tarball。值是一个包名的数组，捆绑的依赖包必须是由 `dependencies` 字段定义的生产依赖。

```json
{
	"name": "pkg-exec",
	"version": "1.0.0",
	"main": "./index.js",
	"dependencies": {
		"isbot": "^5.1.6"
	},
	"bundleDependencies": ["isbot"]
}
```

现在打 tarball 并解压：
```shell
npm pack & tar -xvf ./pkg-ex-1.0.0.tgz
```

查看解压后的目录结构：
```text
.
├── index.js
├── node_modules
│   └── isbot
│       ├── LICENSE
│       ├── README.md
│       ├── index.d.ts
│       ├── index.iife.js
│       ├── index.js
│       ├── index.mjs
│       └── package.json
└── package.json
```

### **overrides**

依赖覆盖。

可以覆盖依赖树中除直接依赖以外的任何依赖（传递依赖）。既可以覆盖依赖的版本，也可以将依赖替换为另一个依赖，还可以指定覆盖依赖树的特定节点范围。

将依赖树中 `foo` 节点下的所有 `loadsh` 都覆盖为 `^2.0.0` 版本。
```json
{
	"overrides": {
		"foo":{
			"lodash":" ^2.0.0"
		}
	}
}
```

或者使用 `.` 来覆盖自身。
```json
{
	"overrides": {
		"foo":{
			".": "1.0.0"
			"lodash":" ^2.0.0"
		}
	}
}
```

不仅覆盖 `foo` 依赖节点下的所有 `lodash` 依赖，还将自身覆盖为 `1.0.0` 版本。

还可以通过 `$` 符来引用直接依赖覆盖目标节点下的传递依赖：
```json
{
	"dependencies": {
		"lodash": "^4.17.21",
	}
	"overrides":{
		"foo":{
			"bar": {
				"lodash":"$lodash"
			}
		}
	}
}
```

## 发布配置

### files

规定包在发布或打 tarball 时应包含的产物（文件或目录）。值是一个文件模式数组，支持 `glob` 匹配风格。

```json
{
	"files": [
	   "src",
	   "!src/**/*.test.*",
	   "types",
	   "compiler",
	   "*.d.ts",
	   "README.md"
	 ]
}
```

对比 `.npmignore` 文件， `files` 字段类似于白名单配置，因此要更加的安全，同时还没有与 `.gitignore` 一起使用时的心智负担。并且不论是 `.gitignore` 还是 `.npmignore` 都无法忽略 `package.json#files` 字段中定义的文件或目录。

如果要匹配所有的文件或目录，可以使用通配符：`*`
```json
{
  "files": ["*"]
}
```

无论如何设置，以下文件总是被打 tarball 并被发布：

- `package.json`
- `README`
- `LICENSE` / `LICENCE`
- `main` 字段中的文件

相反，以下文件总是被忽略：

- `.git`
- `CVS`
- `.svn`
- `.*.swp`
- `.DS_Store`
- `._*`
- `npm-debug.log`
- `.npmrc`
- `node_modules` （注：捆绑依赖除外）
- `config.gypi`
- `package-lock.json`（如果您希望发布，请使用 `npm-shrinkwrap.json`）

### publishConfig

定义包的发布配置。在发布包时，使用 `publishConfig` 中的配置覆盖其它方式定义的配置参数。

> 优先级高于命令行参数与环境变量

```json
{
	"publishConfig": {
		"provenance": true,
		"registry": "<https://registry.npmjs.org>"	
	}
}
```

## 工作区配置

### workspaces

定义工作区，工作区用于管理项目中的子包。值是一个文件模式的数组，支持 `glob` 模式匹配。
```json
{
	"workspaces": [
		"./packages/*"
	]
}
```

## 常见 package.json 扩展字段

由构建工具、开发辅助工具或 Node.js 等对 `package.json` 增加的非 npm 官方字段。

### type

该字段由 Node.js 扩展，被构建工具所支持。

定义包采用的模块格式。

```json
{
	"type":"module",
	"main":"./src/index.js"
}
```

值为 `module` ，则表明包中的模块必须要遵循 ESModule 标准。缺省 `type` 字段或 `type:commonjs` ，将视为 Commonjs 模块。

> 如果模块文件的扩展名为 `.mjs` 或 `.cjs` 那么将无视 `type` 字段的声明。

更多内容请参考：[https://nodejs.org/api/packages.html#type](https://nodejs.org/api/packages.html#type)

### module

该字段由构建工具扩展，例如 Webpack / Rollup 等。

为包指定 ESM 类型的入口文件。

由于 `main` 字段是在 Commonjs 时期定义的，所以在当时很多包都是使用 Commonjs 模块作为包的入口文件，当 ESModule 标准提出后， 为了能更好的兼容之前包，构建工具便新增了同等级的 `module` 字段，专门用于指定 ESM 标准的包入口文件。

```json
{
  "module":"./src/index.esm.js"
}
```

早期一些前端库会同时使用 `module` 与 `main` 字段来分发采用不同模块标准打包的文件。

```json
{
  "main":"./src/index.js",
  "module":"./src/index.esm.js"
}
```

>[!WARNING]
>该字段已不被建议使用，请使用 `type` 字段替代。现如今不论是构建工具还是 Node.js 对 ESModule 标准都有了很好的支持，只要您的软件包不要求必须分发多种模块标准，那么不论是 `main` 还是 `browser` 字段都可以使用任一种模块标准作为包的入口文件。

不同运行时的 ESModule 格式入口文件：
```json
{
  "type":"module",
  "main":"./src/main.esm.js",
. "browser":"./src/main.browser.esm.js"
}
```

不同运行时的 Commonjs 格式的入口文件：
```json
{
	"main": "./src/index.js",
	"browser": "./src/index.browser.js"
}
```

> 不同字段定义的入口点加载优先级：`browser` > `module` > `main` 。

### types

该字段由 TypeScript 扩展，用于指定包的类型声明文件。
```json
{
  "name": "pkg",
  "version": "1.0.0",
  "main": "./index.js",
  "types": "./index.d.ts"
}
```

### exports

该字段由 Node.js 扩展，并被构建工具、Typescript 所支持。

> `Webpack 5.x +` ，`Rollup` 需要使用 `@rollup/plugin-node-resolve` 插件。
> 不同字段定义的入口点加载优先级：`exports` > `browser` > `module` > `main` 。
> 对于旧版本的 Node.js 或构建工具可以与 `main` 字段结合使用以实现兼容。

`exports` 字段规定了 Node.js 或构建工具的模块加载器应如何加载包的入口文件。对比功能有限的 `main` 字段，`exports` 更加强大，它不仅可以为包定义多个入口点，还可以为入口点设定解析条件。同时 `exports` 字段还会阻止包的使用者使用任何未定义的入口点，以提高包的封装性，此举可以更好的帮助包的开发者如何约束包的公共接口形状（shape）。

> 虽然模块加载器无法加载未经 `exports` 定义的入口点，但这并不会阻止你从项目根路径出发以相对地址导入包中的模块文件。 `import { bar } from “./node_modules/pkg-ex/src/sub.js”`

**▪️ 主入口导出（Main Entry Point Export）**

使用上类似于 `main` 字段。
```json
{
	"exports":"./src/index.js"
}
```

**▪️ 子路径导出（SubPath Exports）**

值是一个对象，属性名是对模块路径的别名映射，属性值则是模块位于包中的相对地址。
```json
{
	"name": "pkg-ex"
	"exports": {
		".": "./src/index.js",
		"./sub": "./src/sub.js"
		"./foo": "./src/foo.js"
		"./package.json": "./package.json"
	}
}
```

导入包的模块时：
```jsx
import exPkg from "pkg-ex";
import { sub } from "pkg-ex/sub";
import { foo } from "pkg-ex/foo" 
import "pkg-ex/package.json"
```

> 属性名 `.` 是 `exports` 的导出语法躺(Exports Sugar)，用于在子路径模式下导出包的主入口，等价于 `exports:./src/index.js` 。

▪️ **子路径导出模式（ Subpath Export Patterns）**

支持通配符匹配的子路径导出。以避免包具有大量子路径导出时，使 `package.json` 文件膨胀。
```json
{
	 "name": "pkg-ex",
	 "exports":{
		"./foo/*":"./src/foo/*.js",
		"./bar/*.js":"./src/bar/*js",
		"./bar/internal/*": null
	 }
}
```

在格式上，属性名会以 `*` 号结尾，属性值中也会存在对应的 `*` ，两者一对一的关系。在实际的模块导入时会用匹配到的路径地址替换掉属性值中的 `*` 号，以构成模块在包中的实际地址，最后再交由模块加载器去加载模块文件。
```jsx
import { foo } from 'pkg-ex/foo/index'      // ./src/foo/**index.js**,
import { bar } from 'pkg-ex/bar/index.js'   // ./src/bar/**index**.js
import { internalBar } from 'pkg-ex/bar/internal/index' // NotFound
```

>更推荐无扩展名风格的导出，它具有可读性和掩盖包中文件的真实路径的好处。

**▪️ 条件导出（Conditional Exports）**

条件导出为模块加载器提供了根据不同条件加载特定入口点的能力。

例如，以包的模块格式为条件，选择不同的入口点：
```json
{
	"exports":{
		"import": "./src/index.mjs",
		"require": "./src/index.cjs",
		"default": "./src/backup.js",
	}
}
```

也可以基于运行时环境来选择不同的入口点：
```json
{
	"type": "module",
	"exports": {
		"node": "./src/node/main.cjs",
		"default": "./src/default.js"
	}
}
```

**▪️ 嵌套条件导出（Nested Conditional Exports）**

将运行时条件与模块格式条件的嵌套组合：
```json
{
	"exports": {
		"node": {
			"import": "./src/node/index.mjs",
			"require": "./src/node/index.cjs",
		},
		"default": "./src/main.js"
	}
}
```

或者与子路径进行结合嵌套：
```json
{
  "type":"module",
  "exports": {
		".": {
			"import": "./src/index.js",
			"require": "./src/index.cjs",
			"default": "./src/default.js"
		},
		"./feature":{
			"import": "./src/index.js",
			"require": "./src/index.cjs",
			"default": "./src/default.js"
		}
	}
}
```

TypeScript 还对 `exports` 扩展了 `types` 字段，用于为每个入口点定义类型声明文件。
```json
{
	"exports": {
		".": {
			"default": "./src/index.mjs",
			"types": "./types/index.d.ts"
		}
	}
}
```

看一个 `vue` 的 `exports` 配置实例：
```json
{
  "exports": {
    ".": {
      "types": "./types/index.d.ts",
      "import": {
        "node": "./dist/vue.runtime.mjs",
        "default": "./dist/vue.runtime.esm.js"
      },
      "require": "./dist/vue.runtime.common.js"
    },
    "./compiler-sfc": {
      "types": "./compiler-sfc/index.d.ts",
      "import": "./compiler-sfc/index.mjs",
      "require": "./compiler-sfc/index.js"
    },
    "./dist/*": "./dist/*",
    "./types/*": [
      "./types/*.d.ts",
      "./types/*"
    ],
    "./package.json": "./package.json"
  },
}
```

更多有关模块加载器处理 `exports` 字段的细节，可以查看以下文档：

Webpack：https://webpack.js.org/guides/package-exports/

Node.js | Rollup(node-resolve-plugin)
[https://nodejs.org/api/packages.html#package-entry-points](https://nodejs.org/api/packages.html#package-entry-points)

### unpkg

该字段由 `unpkg` 扩展。

> `unpkg` 是一个全球的内容分发网络（CDN），它会自动同步 npm 上的所有内容。

设置包在 `unpkg` 上的入口文件（默认文件）。由于 CDN 采用的是 URL 访问，因此需要专门为包构建一个 UMD 格式的模块，然后设置 `unpkg` 字段的值为这个模块文件位于包中的相对地址。

```json
{
	"name":"pkg-for-unpkg",
	"version": "1.0.0",
	"unpkg": "dist/index.umd.js"
}
```

这个时候访问：https://www.unpkg.com/pkg-for-unpkg 便会自动跳转到 https://www.unpkg.com/pkg-for-unpkg@1.0.0/dist/index.umd.js

> [!NOTE]
> 由于 CDN 缓存的特性，你可以通过 URL 来访问历史版本下的 `index.umd.js` 文件。

### jsdelivr

该字段由 `jsdelivr` 扩展。设置包在 `jsdelivr` CDN 服务上的入口文件。

> 与 `unpkg` 类似，提供 `npm` 与 `github` 内容的 CDN 服务。

更多信息参见官方文档：[https://www.jsdelivr.com/documentation](https://www.jsdelivr.com/documentation)

### sideEffects

该字段由 Webpack 拓展。

标记整个包的代码或包中模块的代码是否存在副作用，更好的辅助 Webpack 对代码进行树摇(tree-shaking)。

> **副作用：** 模块只导出了成员而没有做其它的操作。比如修改全局变量、打印控制台输出、读写文件、发送网络请求、事件监听、调用其它未导出的函数等等。

值可以是一个布尔值： `true` 表示有副作用，`false` 表示无副作用。
```json
{
	"sideEffects": false
}
```

值也可以是一个文件路径数组，用于表示包的指定模块具有副作用：
```json
{
	"sideEffects": ["./src/sub.js", "*.css"]
}
```

>[!NOTE]
>树摇(tree-shaking)是构建工具提供的一种能力，它可以将导入包中未使用的 `unused` 的代码进行剔除。识别未使用并不是难点，难点是如何确定未使用的代码是否具有副作用，如果有副作用，哪怕未使用，也不能进行 TreeShaking，因此该字段可以让构建工具以更激进的方式进行代码的树摇。

### packageManager

该字段由 Node.js 扩展，由 `corepack` 使用。

> `corepack` 是 Node.js v16.9.0 引入的一款工具，俗称“包管理器的管理器”。使用 `corepack` 可以同时管理 `npm` 、 `pnpm` 、 `yarn` 等。

`corepack` 用于统一前端的包管理器以及版本，以更好的进行团队协作。

```shell
corepack enable        # 开启 corepack 工具
cd ~/your-project      # 进入项目目录
corepack use pnpm@7.x  # 使用 pnpm 包管理器。
```

这个时候项目的 `package.json` 就会新增一个 `packageManager` 字段：
```json
{
  "packageManager": "pnpm@7.33.7+sha256.d1581d46ed10f54ff0cbdd94a2373b1f070202b0fbff29f27c2ce01460427043"
}
```

也可以手动指定要使用的包管理器以及版本。
```json
{
	"packageManager": "pnpm@7.33.5"
}
```

`corepack` 的工作原理很简单，开启 corepack 后，执行包管理器命令会首先被 `corepack` 拦截，`corepack` 会判断目标的包管理器有没有被安装，以及安装的版本符不符合 `pacakgeManager` 字段声明的条件，一旦有一个条件不满足，`corepack` 都会先安装满足条件的包管理器，最终才会调用包管理器执行对应的命令。

> 在开启 `corepack` 的情况下，如果没有指定 `packageManager` 字段便直接运行 `pnpm`，`corepack` 总会下载并运行该包管理器的最新版。

### **browserslist**

该字段由 `browserlist` 扩展，用于确定目标运行时（浏览器、Node.js）的版本。

> 项目无需直接依赖，通常被 `babel` 或 `webpack` 依赖，在 `babel` 或 `webpack` 执行时会读取项目中的 `browserslist` 配置。

```json
{
	"browserslist": ["> 1%", "ie 11", "not dead"]
}
```

### husky(deprecate)

该字段由 Husky 4.x 之前的版本扩展，在 Husky 6.x 之后的版本中被废弃。

> `husky` 是一款 `gitHooks` 的管理工具。

```json
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged",
      "commit-msg": "bun run commitlint --edit $1"
    }
  },
```

### gitHooks(deprecate)

该字段由 Vue 的 `yorkie` 扩展。

`yorkie` fork 自 `husky`，专门为早期版本的 `@vue/cli-service` 服务。

```json
  "gitHooks": {
    "pre-commit": "lint-staged",
    "commit-msg": "node scripts/verify-commit-msg.js"
  },
```

### lint-staged

该字段由 `lint-staged` 扩展。

`lint-staged` 工具可以执行用户自定义的命令来对 git 暂存区的文件进行检查。其优势在于无需对整个项目的所有文件进行检查，弥补了 `husky` 到 `estlint` 或 `stylelint` 间的关键一节。

> `husky → lint-staged → eslint | stylelint`

```json
{
	"lint-staged": {
		"*.{js,jsx,ts,tsx,vue}": ["eslint", "git add"],
		"*.{less,vue}": ["stylelint", "git add"]
  }
}
```

### jest

该字段由单元测试工具 `jest` 扩展。
```json
"jest": {
    "testRegex": "/scripts/jest/dont-run-jest-directly\\\\.js$"
  },
```

## Yarn Package.json 扩展

[https://classic.yarnpkg.com/en/docs/package-json](https://classic.yarnpkg.com/en/docs/package-json)

## Pnpm Package.json 扩展

[https://pnpm.io/package_json](https://pnpm.io/package_json)

## Node.js Package.json 扩展

[https://nodejs.org/api/packages.html#nodejs-packagejson-field-definitions](https://nodejs.org/api/packages.html#nodejs-packagejson-field-definitions)

## Webpack Package.json 扩展

[https://webpack.js.org/guides/package-exports/](https://webpack.js.org/guides/package-exports/)