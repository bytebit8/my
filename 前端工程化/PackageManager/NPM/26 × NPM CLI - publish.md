发布软件包。必须要通过用户身份认证且包的名称和版本在注册表中没有被使用过。

## 身份认证

```shell
➜  app-mono npm login
npm notice Log in on <https://registry.npmjs.org/>
Username: yyyy
Password: xyz
Email: (this IS public) xxxx@gmail.com
npm notice Please check your email for a one-time password (OTP)
Enter one-time password: 1112222
Logged in as yyyy on <https://registry.npmjs.org/>.
```

登录成功后，npm CLI 会自动将 token 写入到用户级 `.npmrc` 配置文件中：
```shell
➜  ~ cat ~/.npmrc 
editor=vim
//registry.npmjs.org/:_authToken=npm_9mTr592J*******
```

## 检查包名是否存在

仅针对新发布包的时候：
```shell
➜  ~ npm search foo20xx
No matches found for "foo20x
```

## 发布公共包

默认发布公共包。

```shell
npm publish 
npm publish --access public
```

## 发布私有包

```shell
npm publish --access restricted
```

## 发布具有出处的包

```shell
npm publish --provenance
```

## 发布具有分发标签的包

使用 `--tag` 参数可以在发布包时为当前版本标记分发标签，相当于就是为版本号取一个别名。
```shell
npm publish --tag latest
```

> 默认就是 `latest` .


## 惰性发布

主要用于包的发布调试。

通过使用 `--dry-run` 参数可以以日志的形式输出发包的信息并不会正式的提交包到注册表中。

```shell
➜  app-mono npm publish --dry-run
npm notice 
npm notice 📦  app-mono@0.1.0
npm notice === Tarball Contents === 
npm notice 0B   README.md                     
npm notice 460B package.json                  
npm notice 346B packages/app1/package.json    
npm notice 62B  packages/app1/scripts/build.js
npm notice 204B packages/app2/package.json    
npm notice === Tarball Details === 
npm notice name:          app-mono                                
npm notice version:       0.1.0                                   
npm notice filename:      app-mono-0.1.0.tgz                      
npm notice package size:  570 B                                   
npm notice unpacked size: 1.1 kB                                  
npm notice shasum:        6fe947fc4d87746501f61185888ee99a03f12468
npm notice integrity:     sha512-3Jckmv2Q+zhXF[...]owIYAaTGqKZDw==
npm notice total files:   5                                       
npm notice 
npm notice Publishing to <https://registry.npmjs.org/> with tag latest and default access (dry-run)
+ app-mono@0.1.0
```

该命令的输出日志类似于 `npm pack` 命令，区别是不会真的在本地产生一个 `tarball` 。

## 工作区支持

- `npm publish` 命令默认只向注册表发布当前包。
- 使用 `-w` 参数可以向注册表发布指定的子包。
- 使用 `-ws` 参数可以将所有子包发布到注册表中。

```bash
npm publish
npm publish -w packages/app1
npm publish -ws
```

## 常用参数
| 参数             | 说明                                         |
| -------------- | ------------------------------------------ |
| `--tag`        | 为当前发布的版本添加分发标签 `dist-tag` 。                |
| `--access`     | 发布范围包。<br>public - 公共包<br>restricted - 私有包 |
| `--provenance` | 发布具有出处的包（需要构建流程支持）                         |
| `--dry-run`    | 调试包的发布，不会产生实体更改。                           |
| `--opt`        | 如果开启了双因素（2FA）认证，那么发包时需要通过该参数指定。            |
| `--loglevel`   | 日志等级。`verbose`                             |
| `--timing`     | 计时信息。                                      |
| `-w`           | 变更指定工作区的版本。                                |
| `-ws`          | 变更所有工作区的版本。                                |
