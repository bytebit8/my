使用符号链接(Symbol Link)来安装本地依赖。通过创建符号链接将本地依赖链接当前项目的 `node_modules` 目录中，这非常便于包的开发和调试，节省重建流程。

## 对比 install 命令

`npm link` 与 `install` 命令在功能上类似，都是用来添加依赖到项目中，一个是通过符号链接的方式另一个则是真实下载依赖的方式。下面是与 `install` 命令的具体对比：

- `npm link` 会使用符号链接将本地依赖链接到项目的 `./node_modules` 目录中，而不会真实的下载包。
- `npm link` 默认不会将依赖信息记录到 `package.json` 与 `package-lock.json` 文件中。

相同点有：

- 都会将包中的 `bin` 程序以符号链接的方式连接到 `./node_modules/.bin` 目录中。

## 安装本地依赖

现在，给定项目 `app-mono` ，通过 `npm link` 来安装一个本地的包 `pkg`, 包对外提供了一个 `bin` 程序，包的相对地址为：`../app-pkg`：

```shell
➜  app-mono npm link ../app-pkg 
```

使用 `npm ls` 命令查看当前项目的依赖列表：
```shell
➜  app-mono npm ls pkg
app-mono@0.1.0 /Users/xxx/Desktop/Study/app-mono
└── pkg@npm:app-pkg@2.4.5 extraneous -> ./../app-pkg
```

或者进入到 `node_modules` 下查看。
```shell
➜  node_modules ls -lha
total 80
drwxr-xr-x@ 79 xxxx  staff   2.5K Apr 21 21:37 .
drwxr-xr-x@ 11 xxxx  staff   352B Apr 19 10:45 ..
lrwxr-xr-x@  1 xxxx  staff    13B Apr 21 21:37 pkg -> ../../app-pkg

➜  node_modules ls -lha .bin 
lrwxr-xr-x@  1 xxxx  staff    25B Apr 21 21:37 pkg -> ../pkg/scripts/app-pkg.sh
```

现在修改 `app-pkg` 目录下的 `pkg` 包的内容，将实时的反应到依赖其的 `app-mono` 项目中。

## 安装全局依赖

`npm link` 命令还可以将全局依赖链接到项目的 `./node_modules` 目录中，同时连接其 `bin` 程序。

在 `app-mono` 项目下执行下面命令；

```shell
npm i chalk-cli -g
npm link chalk-cli
```

使用 `npm ls chalk-cli` 查看依赖：
```shell
➜  app-mono npm ls chalk-cli
app-mono@0.1.0 /Users/xxx/Desktop/Study/app-mono
└──  chalk-cli@5.0.1 extraneous -> ./../../../.nvm/versions/node/v18.18.2/lib/node_modules/chalk-cli
```

>`extraneous` 表示外部的依赖

## 将本地依赖添加全局

进入到本地 `pkg` 包中，执行下面的命令：
```shell
npm link 
```

现在执行 `npm ls -g` 列出全局依赖
```shell
➜  ~ npm ls -g
/Users/xxx/.nvm/versions/node/v18.18.2/lib
├── chalk-cli@5.0.1
└── **pkg@0.1.0 -> ./../../../../../Desktop/Study/app-pkg**
```

现在可以发现本地依赖已经链接到了全局 `$PREFIX/lib/node_modules` 目录中。

进入到 `app-mono` 项目中，还可以将全局依赖链接到本地项目中

```shell
npm link pkg
```

## 常用参数
| 参数    | 说明         |
| ----- | ---------- |
| `-w`  | 废弃指定的子包。   |
| `-ws` | 废弃工作区所有子包。 |
