发音 `/eg.zek/`。
执行目标包对外提供的 `bin` 程序（**二进制可执行文件**或**脚本**）

使用格式主要有以下几种：
```bash
npm exec -- pkg-name
npm exec --package pkg-name <command> [args]
npm exec --pacakge pkg-1 --package pkg-2 -- <pkg1-command> [args] & <pkg2-command> [args]
npm exec -c "echo \\"Hello\\""
npm exec --pacakge pkg-1 --package pkg-2 -c "pkg1-command & pkg2-command"
```

## 常用别名

```shell
npx
x
```

> NPM 从 7.x 开始为了规范 `npx` 命令，将其底层实现替换为了 `exec` 命令。

## 包名作为命令名称

给定一个测试包 `npm-exec-test` ，其 `package.json` 内容如下：
```json
{
  "name": "npm-exec-test",
  "version": "1.0.0",
  "bin": {
    "npm-exec-test": "./bin/npm-exec-hello.mjs"
  }
}
```

包名与命令名称相同，因此可以直接在 `exec` 命令后面跟包名，直接执行包的命令：
```shell
npm exec npm-exec-test
```

以 `eslint` 包为例：
```shell
npm exec eslint .
```

查看其 `bin` 命令：
```shell
➜  ~ npm view eslint bin
{ eslint: 'bin/eslint.js' }
```

## 包名与命令名称不同

分两种情况：
- 包只提供了一个 `bin` 命令。
- 包提供了多个 `bin` 命令。

**1. 包只提供了一个 `bin` 命令**

```shell
npm exec chalk-cli bold red "hello"
```

查看 `chalk-cli` 包的命令：
```shell
➜  ~ npm view chalk-cli bin
{ chalk: 'cli.js' }
```

**2. 包提供了多个 `bin` 命令**

这个时候，必须要在 `exec` 命令附加 `--package` 选项，表明要安装的包，同时指定要执行的命令。

查看包提供的 `bin` 命令：
```shell
➜  ~ npm view typescript bin
{ tsc: 'bin/tsc', tsserver: 'bin/tsserver' }
```

执行 `tsc` 命令：
```shell
npm exec --package typescript -- tsc --init
```

## 同时执行一个包的多条命令

使用 `-c` 命令选项来执行包中的多个命令。
```shell
npm exec --package typescript -c "tsc --init & tsserver"
```

## 同时执行多个包的多条命令

现在同时执行 `figlet` 与 `chak` 命令，来给输出的内容添加样式：
```shell
npm exec --package figlet --package chalk-cli -- figlet "hello" | chalk red bold --stdin
```

也可以使用 `--call` 参数来调用多个包中的命令：
```shell
npm exec --package figlet --package chalk-cli -c "figlet 'hello' | chalk red bold --stdin"
```

>[!NOTE]
>`--` 双连字符主要起到辅助 npm 命令停止解析的开关作用。 对于 `npm exec` 命令而言，双连字符后面的内容将不再被视为命令的配置选项，而是命令的附加参数，这些附加参数会被 `exec` 完整的放入到 `shell` 中执行。

因此，上面的命令执行，大致可以用下面的命令来模拟执行：
```shell
npm i figlet chalk-cli
npm pkg set scripts.custom "figlet 'hello' | chalk red bold --stdin"
npm run custom
```

## 缺省 --package 选项

`--package` 选项的功能主要是安装包，只有包事先安装好后，才能调用包中的命令。

如果只是执行一个包的命令，那么可以缺省 `--package` 选项，`npm exec` 命令默认会尝试使用接收的第一个参数作为包名和命令名称去安装与执行。
```shell
npm exec npm-exec-test 
```

分解动作：
```shell
npm install npm-exec-test
cd ./node_modules/bin
npm-exec-test
```

如果包的命令与包名并不相同，只要 `bin` 字段声明的命令只有一条，也可以缺省 `--package` 选项 `exec` 也能直接调用包的命令：
```shell
➜  ~ npm exec chalk-cli green hello
hello
➜  ~ npm info chalk-cli bin
{ chalk: 'cli.js' }
```

如果包提供的 `bin` 命令有多个，那么 `--package` 选项不能缺省。
```shell
npm exec --package typescript -- tsc --init
```

若要同时调用一个包的多条命令或多个包的多条命令，可以使用 `exec` 命令的 `-c` 选项来交由 Shell 程序执行。

## exec 命令工作原理

`npm exec` 命令的工作原理其实非常简单， 简而言之，安装依赖然后执行其命令。

`npm exec` 命令会将 `--package` 选项指定的包安装到缓存目录 `~cache/_npx` 中，你可以通过 `npm config` 命令获取当前 npm 的 `cache` 的目录地址：
```shell
➜  ~ npm config get cache
/Users/xxx/.npm
```

现在进入到 `~/.npm/_npx` 目录中：
```shell
➜  _npx tree -L 2
.
├── 42046064dc62817d
│   ├── node_modules
│   ├── package-lock.json
│   └── package.json
├── 71456b2f4dc260cd
│   ├── node_modules
│   ├── package-lock.json
│   └── package.json
```

其中 `42046064dc62817d` 目录就是我们在执行 `npm exec --package npm-exec-test --package chalk-cli` 命令时创建的，查看其 `package.json` 内容：
```shell
➜  42046064dc62817d cat package.json 
{
  "dependencies": {
    "npm-exec-test": "1.0.0",
    "chalk-cli": "^5.0.1"
  }
}
```

可以看到确实是之前安装的两个依赖。再查看 `./node_modules/.bin` 目录，进一步确认无误。
```shell
➜  42046064dc62817d ls -lh ./node_modules/.bin 
total 0
lrwxr-xr-x  1 taptap  staff    19B  4 27 20:11 chalk -> ../chalk-cli/cli.js
lrwxr-xr-x  1 taptap  staff    38B  4 27 20:56 exec-test -> ../npm-exec-test/bin/npm-exec-hello.mjs
```

现在我们已经知道了 `npm exec` 命令关于包的安装位置与流程了，那么 `exec` 命令又是如何运行包的命令的呢？

现在进一步改造假定的 `npm-exec-test` 包，修改 `package.json` 内容如下：

```bash
{
  "name": "npm-exec-test",
  "version": "1.0.0",
  "bin": {
    "npm-exec-test": "./bin/npm-exec-test.mjs",
    "npm-exec-hello": "./bin/npm-exec-hello.mjs"
  }
}
```

`npm-exec-test.mjs` 文件内容如下：

```jsx
#!/usr/bin/env node
console.log("------------ARGV------------");
console.log(process.argv);
console.log("------------PATH------------");
console.log(process.env.PATH?.split(':'));
```

然后执行以下命令查看输出内容：

```shell
npm exec --package npm-exec-test npm-exec-test -- flags
```

```shell
------------ARGV------------
[
  '/Users/xxx/.nvm/versions/node/v18.18.2/bin/node',
  '/Users/xxx/.npm/_npx/71456b2f4dc260cd/node_modules/.bin/npm-exec-test',
  'flags'
]
------------PATH------------
[
  '/Users/xxx/.npm/_npx/71456b2f4dc260cd/node_modules/.bin',
  '/Users/xxx/node_modules/.bin',
  '/Users/node_modules/.bin',
  '/node_modules/.bin',
  '/Users/xxx/.nvm/versions/node/v18.18.2/lib/node_modules/npm/node_modules/@npmcli/run-script/lib/node-gyp-bin',
  '/Users/xxx/.nvm/versions/node/v18.18.2/bin',
  '/usr/local/bin',
  '/usr/bin',
  '/bin',
  '/usr/sbin',
  '/sbin',
  '/usr/local/bin'
]
```

现在彻底明白了，当 `exec` 命令将包安装到缓存目录后，为了能够在命令行中执行包中的命令，会将包的 `./node_modules/.bin` 目录的完整地址加入到当前 Shell 环境的 `PATH` 变量中。而双连字符后面的内容则会作为 `npm-exec-test` 命令的参数传入。

> 缓存目录的目录名是通过 `hash` 生成的，主要是基于包名的组合。

现在我们可以基于对上述知识点的掌握，来解读下面命令的执行情况：

```shell
npm exec --package npm-exec-test --package chalk-cli -- npm-exec-test | chalk bgGreenBright --stdin
```

1. 在 `~/.npm/_npx` 目录下用 `hash` 值创建一个唯一的目录来安装依赖 `npm-exec-test` 与 `chalk-cli` 。
2. 将目录内的 `./node_modules/.bin` 完整路径加入到当前 Shell 环境的 `PATH` 变量中。
3. 将双连字符后面的内容作为 `npm exec` 命令的参数，然后再完整的交由 shell 执行：` npm-exec-test | chalk bgGreenBright --stdin`
4. 最后，shell 命令执行的过程中，`bgGreenBright --stdin` 将作为 `chalk` 命令的参数。

## 执行项目中已安装依赖的命令

`npm exec` 命令可以直接运行项目 `./node_modules/.bin` 目录中的命令。这无疑比 `npm run` 命令结合 `scripts` 字段更加方便。

```shell
mkdir demo
cd demo 
npm init -y
npm i eslint -D

npm exec eslint .
```

## 执行本地依赖的命令

```bash
npm exec ../pkg/npm-exec-test
npm exec --package ../pkg/npm-exec-test npm-exec-hello
```

## 工作区支持

`npm exec` 支持工作区相关的选项，可以通过 `-w` 或 `-ws` 等命令选项，指定命令作用的子包或根包。

## 常用参数**
| 参数名          | 说明              |
| ------------ | --------------- |
| `--package`  | 安装指定的依赖，可以指定多个。 |
| `-c`         | `--call`        |
| `--loglevel` | 日志等级。`verbose`  |
| `--timing`   | 计时信息。           |
| `-w`         | 废弃指定的子包。        |
| `-ws`        | 废弃工作区所有子包。      |
