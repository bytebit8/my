
“脚本” 是在 `package.json#scripts` 字段中定义的一条命令.用于执行相应的程序（可执行文件或脚本文件）。

## 内置脚本

内置脚本可以省略 `run` 命令，脚本命令对应的程序可以被 npm 直接执行。
```shell
npm install
npm start
npm stop
npm restart
npm test
npm version
npm publish
```

还有两个比较特殊的内置命令，它们不会触发 `scripts` 字段中声明的同名命令：
```shell
npm pack
npm rebuild 
```

## 自定义脚本

开发者可以在 `package.json#scripts` 字段中创建自定义的脚本命令，自定义脚本必须要通过 `npm run` 命令执行。

```json
{
  "scripts":{
    "compress": "node ./scripts/compress.js"
  }
}
```

```bash
npm run compress
```

## 前置 & 后置脚本

所有的脚本命令都具有前置/后置脚本。它与内置脚本还是自定义脚本无关。
前置/后置脚本分别会在脚本命令执行之前和执行之后执行。
为脚本命令创建前置脚本或后置脚本只需要在命令名称前加 `pre` 或 `post` 前缀即可。
```json
"scripts": {
  "precompress": "node ./scripts/pre_compress.mjs",
  "compress": "node ./scripts/compress.mjs",
  "postcompress": "node ./scripts/post_compress.mjs"
}
```

执行 `npm run compress` 命令时，会在其之前执行 `npm run precompress` 命令，然后在其之后执行 `npm run postcompress` 命令。

## 生命周期脚本

“生命周期脚本”是一系列在满足特定事件下连续触发的多个脚本。其组成主要有：

- 前置/后置脚本。
- `prepare` 脚本。
    在安装(`install`)、打包(`pack`)、发布(`publish`) 之前运行。
- `prepublish` 脚本。
- `prepublishOnly` 脚本。
- `dependencies` 脚本。
    当项目的 `node_modules` 目录发生更改后运行。

>[!note]
>自 npm 7.x 起出于性能考量，对于 `prepare` 和 `dependencies` 脚本默认只会在后台执行，如果要在前台打印日志输出，请附加 `--foreground-scripts` 参数运行。

例如，执行 `npm install` 或 `npm ci` 会触发以下系列的生命周期脚本：
```shell
preinstall
install
postinstall
prepublish
preprepare
prepare
postprepare
```

>需要保证 `npm install` / `npm ci` 命令无参执行。

`npm pack` 会触发以下生命周期脚本：
```shell
prepack
prepare
postpack
```

`npm cache add` 与 `npm diff` 则只会触发：
```shell
prepare
```

>`prepare` 不会在 `--dry-run` 期间运行

`npm publish` 命令会比较特殊，它不会触发 `prepublish` 脚本，但会触发一个 `prepublishOnly` 脚本。
```shell
prepublishOnly
prepack
prepare
postpack
publish
postpublish
```

>[!note]
>原因是早期在设计上存在命名歧义，使用者很自然的用 `prepublish` 脚本来处理发布前的一些操作，但问题在于 `install` 、 `ci` 等命令的生命周期脚本也会触发该命令，这样便会产生冲突。为了解决这个问题 ，npm 在后续的版本中引入了 `prepare` 生命周期脚本，专门用于执行一些准备动作。同时又废弃了 `npm publish` 命令的前置 `prepublish` 脚本，使用 `prepublishOnly` 进行临时替代。

最后其余的内置脚本或用户自定义脚本，它们的生命周期脚本就是其前置与后置脚本。
```text
pre<command-name>
<command-name>
post<command-name>
```

## 环境变量

NPM 脚本命令在执行时会创建一些环境变量。

### PATH

得益于 NPM 脚本命令会在执行期间将项目的 `./node_moduels/.bin` 路径加入到 环境变量 `PATH` 中，从而使我们可以在脚本命令中直接调用那些具有对外提供命令的 npm “包”。
```json
{
  "scripts":{
    "start":"echo $PATH"
  }
}
```

```shell
> echo $PATH

/Users/xxx/Desktop/Study/app-mono/node_modules/.bin:
/Users/xxx/Desktop/Study/node_modules/.bin:
/Users/xxx/Desktop/node_modules/.bin:
/Users/xxx/node_modules/.bin:
/Users/node_modules/.bin:
/node_modules/.bin:
/Users/xxx/.nvm/versions/node/v16.18.1/lib/node_modules/npm/node_modules/@npmcli/run-script/lib/node-gyp-bin:
/usr/local/bin:/System/Cryptexes/App/usr/bin:/usr/bin:/bin:/usr/sbin:/sbin:
/var/run/com.apple.security.cryptexd/codex.system/bootstrap/usr/local/bin:
```

因此你可以直接在命令中执行其它依赖提供的包：
```shell
{
	"devDependencies": "chalk@^5.3.0"
	"scripts":{
		"start":"chalk red red"
	}
}
```

### npm_package\_*

npm 脚本命令还会在执行时将 `package.json` 中的字段解析为 `npm_package_*` 的格式，然后加入到环境变量中：
```shell
{
	"scripts":{
		"test":"echo $npm_package_name \\n echo $npm_package_version"
	}
}
```

>这在一些 CI 环境中会比较有用。

## 实践建议

1. 除非确实有必要，否则不要以非零错误代码退出。除了卸载脚本，这会导致 npm 操作失败，并可能被回滚。如果失败是小问题或者只会阻止一些可选功能，最好打印一个警告并成功退出。
2. 尽量不要使用自定义的脚本去做 npm 能为你做的事情。
3. 检查环境变量或配置参数以确定放置事物的位置。
4. 脚本总是从项目的根目录运行，无论调用 npm 时当前工作目录是什么。如果你想根据你所在的子目录来改变脚本的行为，你可以使用 `INIT_CWD` 环境变量，它保存了你运行 `npm run` 时所在的完整路径。