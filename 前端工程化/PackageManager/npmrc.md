- npm 的配置文件。
- npm 支持多种配置方式：环境变量、命令行参数、配置文件。
- npmrc 配置文件按照优先级分为：全局配置文件 `->` 用户配置文件 `->` 项目配置文件。
### 文件格式

npm 的配置文件是一种 [`ini`](https://zh.wikipedia.org/wiki/INI%E6%96%87%E4%BB%B6) 格式的文件，文件内容以 `key=value` 的形式保存。
```ini
audit-level=high
```

配置文件中可以使用 `${VAR}` 的格式来替换为环境变量值。
```ini
cache=${HOME}/.npm-cache
```

> 更多的配置内容可以参考 [[16 × 配置参数]]

### 注释格式

使用 `;` 或 `#` 作为配置文件的注释内容：
```ini
; "user" config from /Users/xxx/.npmrc

//registry.npmjs.org/:_authToken = (protected) 
audit-level = "high" 
```

### 用户认证配置

必须要与限定的注册表结合使用，这可确保 NPM 永远不会将凭据发送到错误的主机上。
```
registry=https://registry.npmjs.org
@scope:registry=https://registry.scope.com

//registry.npmjs.org:_authToken=${NPM_TOKEN}
//registry.scope.com:_auth=${NPM_BASE64_TOKEN}
```

`:_authToken` ：npm 用户的认证令牌（Access Token）。
`:_auth` ：base64 形式的认证字符串。

### 编辑配置文件

可以手动编辑配置文件：
- 项目配置文件：直接编辑项目根目录下的 `.npmrc` 文件。
- 用户配置文件：编辑 `~/.npmrc` 文件。
- 全局配置文件：编辑 `${PREFIX}/etc/npmrc` 文件。

也可以使用 `npm config set` 命令来编辑。如果是在项目根目录下执行则修改项目配置文件，否则默认修改用户配置文件，加上 `-g` 参数可以编辑全局配置文件。