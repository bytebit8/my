## 安装 NPM

安装 NPM 实际上是安装 npm CLI，即 NPM 客户端程序。

### Node.js 捆绑安装

Node.js 默认捆绑了 NPM，因此安装 Node 时会自动带有 NPM。

### 使用 nvm 安装 

nvm (Node Version Manger) Node 的版本管理器。
使用 nvm 可以在本地安装和管理多个不同版本的 Node.js。这样便可以实现多个 NPM 版本的本地切换了。

> 推荐使用 [nvm](https://github.com/nvm-sh/nvm) Node 版本管理器。

## 更新 NPM 版本

NPM 支持自更新。
```bash
npm install npm@latest -g
```

## 登录 NPM

### 登录方式

NPM 提供了两种登录方式，开发者可以选择是通过站点(npmjs.com) 还是客户端(npm CLI) 进行登录。

**站点(npmjs.com):**
- 用户名/密码 + 认证方式进行登录。

**客户端(npm CLI):**
- `npm login` 命令 + 用户名/密码 + 认证方式。
```shell
➜  ~ npm login
npm notice Log in on https://registry.npmjs.org/
Username: xxxx
Password: 
Email: (this IS public) xxx@yy.com
npm notice Please check your email for a one-time password (OTP)
Enter one-time password: 36956200
Logged in as xxxx on https://registry.npmjs.org/.
```
- `npm login` 命令 + 站点会话
```shell
➜  ~ npm login --auth-type=web
npm notice Log in on https://registry.npmjs.org/
Authenticate your account at:
https://www.npmjs.com/login?next=/login/cli/e866d74e-6223-4e3e-909d-2dd5348630be
Press ENTER to open in the browser...

Logged in on https://registry.npmjs.org/.
```

### 当前用户

使用 `npm who` 命令可以返回当前登录的用户名。
```shell
npm whoami;
```

### 认证方式

>[!warning]
>一旦开启 2FA 认证，需要妥善保存 Recovery Code。如果 2FA 设备 + 恢复码均丢失，只能联系 NPM 官方人员手动重置账号。

NPM 支持的认证方式有以下两种：
- 知识因素：用户名 + 密码。
- 双因素 ([[一文搞懂 OTP 双因素认证  monchickey 探索和分享计算机技术]])（2FA, Two-Factor Authentication）：
	- 一次性密码认证 (OTP, One-Time Password)：NPM 默认的方式，系统会发送一次性密码到您的邮箱中。
	- 基于时间戳的一次性密码认证（TOTP）： 需要在账号管理中开启 2FA 认证并绑定到具体的设备中。