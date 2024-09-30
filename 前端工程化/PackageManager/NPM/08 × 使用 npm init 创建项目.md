npm CLI 的 `init` 命令除了可以创建 `package.json` 文件来初始化 “npm 包”外，还可以调用“生成器程序（Initializer）” 来创建项目（包）。“生成器程序”的更通俗称呼就是 —— “脚手架工具”。

以下是常见的脚手架工具：
- React 的 `create-react-app`
- Vue 的 `create-vue`
- Vite 的 `create-vite`
- Astro 的 `create-astro`
- Solid-js 的 `create-solid`

在早期（npm7.x之前），通常使用 `npx` 命令来调用脚手架工具快速创建项目：
```shell
npx create-react-app my-app
npx create-vue my-vue-app
npx create-vite my-vite-app
```

现在，你可以使用 `init` 命令以包名简写的方式调用生成器程序：
```shell
npm init react-app my-app
npm init vue my-vue-app
npm init vite my-vite-app
```

这是因为 `npm init` 命令会默认在包名的前面添加一个 `create-*` 前缀。其次，`init` 命令的底层调用的是 `npm exec` 命令，它会自动调用包中与包名相同的 `bin` 程序。而这个 `bin` 程序就是生成器程序：
 ```shell
npm info create-react-app bin
# { 'create-react-app': 'index.js' }
```
 
所以，执行 `npm init create-foo my-app` 就相当于执行：
```shell
npm exec --package create-foo -- --name=my-app
```

> `--` 双连字符分隔后的内容表示传递给被执行程序的命令参数。

>[!note]
>在早期更多是使用 `npx` 命令来执行包的 `bin` 程序来调用生成器程序来创建项目，到了 npm 7.x 之后为了提供一致的用户体验，便使用 `npm exec` 命令替代 `npx` 命令。对于生成器的使用则移动到了 `npm init` 命令上。

`init` 命令的别名(Alias)可以写作为 `create`，所以上面的命令，还可以写作为：
```shell
npm create vue my-vue-app
npm create react-app my-app
```
