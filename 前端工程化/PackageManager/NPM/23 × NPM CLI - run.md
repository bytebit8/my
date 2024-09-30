
主要用于运行自定义脚本命令。运行内置脚本命令可以省略该命令。

## 创建自定义脚本

给定一个 `package.json` 文件，其 `scripts` 字段定义如下：

```json
{
 "scripts": {
    "test": "echo \\"Error: no test specified\\" && exit 1",
    "build": "echo \\"This is a build Script Program\\""
  }
}
```

## 向脚本程序传参

使用 `--` 双连字符可以为命令的脚本程序传递参数。

```json
"scripts": {
  "build": "./scripts/build.js -- --type=prod"
}
```

`build.js` 的内容:

```javascript
#!/usr/bin/env node
console.log(process.argv);
```

执行 `npm run build` 的输出：

```jsx
[
  '/Users/xxx/.nvm/versions/node/v18.18.2/bin/node',
  '/Users/xxx/Study/app-mono/scripts/build.js',
  '--',
  '--type=prod'
]
```

注意区分为 `npm run` 命令传参和为命令脚本程序传参两者的区别。为 `npm run` 命令传参，需要事先查看该命令支持的参数，然后在执行命令时附加命令选项：
```shell
npm run -h
npm run build --ignore-scripts
```

## 脚本命令与环境变量

与内置脚本命令相同行为，`run` 命令在执行时也会将项目的 `./node_modules/.bin` 路径加入到临时的系统环境变量 `PATH` 中，因此，自定义脚本程序也可以调用 npm 包对外提供的可执行命令 —— `bin` 程序。

此时 `build.js` 内容如下：
```javascript
#!/user/bin/env node
console.log(process.env.PATH.split(':'));
```

执行 `npm run build` 输出结果：
```shell
➜  app-mono npm run build

# > ./scripts/build.js

#[
#  '/Users/xxx/Study/app-mono/node_modules/.bin',
#  '/Users/xxx/Study/node_modules/.bin',
#  '/Users/xxx/node_modules/.bin',
#  '/Users/xxx/node_modules/.bin',
#  '/Users/node_modules/.bin',
#  '/node_modules/.bin'
#]
```

>[!NOTE]
>从输出结果可知 `npm run` 命令会从项目当前的 `./node_modules/.bin` 位置开始，一直向上遍历完用户 `Users` 目录，最后直到系统根目录下的 `/node_modules/.bin` 为止。

除此之外，执行 `run` 命令时，还会解析 `package.json` 文件的字段，以 `npm_package_*` 为固定前缀格式，加入到环境变量中。
```json
{
  "scripts":{
    "build":"echo $npm_package_name"
  }
}
```
 
## 在脚本程序中调用包的 `bin` 程序

正是得益于 `run` 命令会将项目的 `./node_modules/.bin` 路径加入到环境变量 `PATH` 中，因此现在我们可以在命令的脚本程序中直接调用本地依赖的可执行程序了（即依赖 `package.json#bin` 字段声明的 `bin` 程序）。

首先，安装本地依赖：
```shell
➜  app-mono npm i chalk-cli -D
```


按照最常规的方式，可以编写个 Shell 脚本 —— `build`
```shell
#!/bin/bash
echo "Hello World" | chalk bold red --stdin
```

修改 `package.json#scripts` 字段为：
```json
{
 "build":"sh build"
}
```

也可以编写 Node 脚本：
```javascript
#!/user/bin/env node
const { spawn } = require("child_process");

// 使用 spawn 方法执行命令，并设置环境变量
const command = spawn(
  "sh",
  ["-c", 'echo "Hello World!" | chalk bold red --stdin'],
  {
    env: { ...process.env, FORCE_COLOR: "1" },
  }
);

// 监听 stdout 数据事件
command.stdout.on("data", (data) => {
  // 直接输出到控制台，保持颜色码
  process.stdout.write(data);
});

```

或者直接将要执行的 shell 脚本内置到 `package.json#script` 字段中：
```json
"scripts": {
  "build": "echo \\"This is a build script.\\" | chalk --stdin bgRed bold italic underline"
}
```

>[!NOTE]
>命令程序中的关键 `chalk --stdin bgRed bold italic underline` 实际上就是对 `./node_modules/.bind/chalk --stdin bgRed bold italic underline` 的替代。

## 工作区支持

- `npm run` 命令默认只运行根包中的命令。
- 若当前位置位于某个子包，则只会执行当前子包的命令。
- `-ws` 参数可以运行工作区中所有子包的命令（但不会运行根包中的命令）。
- `-w` 参数可以过滤子包，只运行指定子包中的命令。

```shell
npm run build                   # 只运行根包中的命令 或 当前子包的命令。
npm run -ws build               # 运行工作区中所有子包的命令（除根包）。
npm run build -w packages/app1  # 运行指定子包中的命令。
```

如果要执行的命令恰好在某些工作区中没有配置，则可以使用 `--if-present` 参数来静默执行。
```shell
npm run build -ws --if-present
```

## 常用参数
| 参数                         | 说明                              |
| -------------------------- | ------------------------------- |
| `--wrokspaces`             | `-ws`                           |
| `--workspace`              | `-w`                            |
| `--include-workspace-root` | 执行的动作包含根包。                      |
| `--if-present`             | 静默执行命令。                         |
| `--ignore-scripts`         | 忽略命令的 `pre` 与 `post` 等命令钩子。     |
| `--foreground-scripts`     | 将一些默认在后台运行的生命周期脚本，放在<br>前台运行输出。 |
| `--script-shell`           | 用于自定义命令行程序，例如 `bash` 等。         |
| `--loglevel`               | 日志等级。`verbose`                  |
| `--timing`                 | 计时信息。                           |