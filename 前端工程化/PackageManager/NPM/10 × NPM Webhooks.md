## 什么是 Hook ?

**Hook** 称之为“钩子”或“挂钩”。对于一些采用可扩展设计的系统、程序、插件或 API，会在其执行的若干关键节点暴漏一些钩子，以允许开发者在需要时插入（挂钩）自己的代码，执行自定义的流程，扩展原有程序的功能。例如 vue、react 的生命周期回调，npm 脚本命令的 pre/post 等前置后置命令......

## Hook 的设计模式

**Hook** 的实现方式最常用的是 “发布/订阅（Pub/Sub）模式”。一个或多个订阅者（Subscribers）订阅一个主题（Topic）；发布者(Publishers)可以编辑这个主题，一旦主题发生变更时，所有订阅者都会收到通知。

## Hook 的表现形式

**Hook** 的表现形式有多重，可以是一段程序（函数、脚本），也可以是一段 URL 地址。

## 什么是 WebHook？

**Webhook** 顾名思义便是由 Web Application 提供的 hook 能力，其表现形式为一段 URL 地址，也被称之为 “URL Endpoint”。

WebHooks 本质上是一个 HTTP 回调或推送，以获取应用程序在特定节点下状态变更后的实时信息。简单来说，就是当某个事件发生时通过向指定的 URL 发送 HTTP 请求来发布通知。

```shell
curl https://webhook.site/#!/view/11ae9ec2-6ab0-469d-a1b2-ee773c9f5bdb
```

> `webhook.site` 提供了免费的 URL Endpoint / Email Endpoint / DNS Endpoint 等服务。并支持在线调试查看。

借助 Webhooks 的能力我们可以将不同的网络服务连接起来，以实现自动化的工作流程。例如，触发CI/CD流程、发送通知到聊天应用、更新文档等。

Webhook 的优点在于它提供了一套简单而有效的方式来实现服务间的通信和数据同步，不需要频繁轮询或者其他复杂的同步机制。

## 如何使用 NPM Hook?

NPM Hook 顾名思义就是 NPM 提供的 Hook 能力，它属于 WebHooks，在 NPM Hooks 的事件主要与“包”的变更有关，例如包的发布、废弃、删除等。

>[!WARNING]
>该命令需要用户身份验证且只有付费账户才能实际使用。

### npm hook ls

```shell
npm hook ls 
npm hook ls lodash
```

```text
➜  ~ npm hook ls
You have one hook configured.
┌──────────┬────────┬───────────────────────────────────────────────────────────────────┐
│ id       │ target │ endpoint                                                          │
├──────────┼────────┼───────────────────────────────────────────────────────────────────┤
│ xjcilj1k │ lodash │ <https://webhook.site/#!/view/11ae9ec2-6ab0-469d-a1b2-ee773c9f5bdb> │
│          ├────────┴───────────────────────────────────────────────────────────────────┤
│          │ never triggered                                                            │
└──────────┴────────────────────────────────────────────────────────────────────────────┘
```

### npm hook add

```shell
npm hook add vue <URL Endpoint> "my-npm-webhook-sceret"
```

### npm hook rm

```shell
npm hook rm xjcilj1k
```

## 签名校验

使用 Webhook 时需要考虑安全性和错误处理的情况。下面是使用 Express 编写的接口服务用于接收 NPM Webhook 下发的订阅服务。

```jsx
const express = require("express");
const bodyParser = require("bodyParser");
const crypto = require("crypto");

const app = new express();
const PORT = 3000;
const XNPMSignature = "x-npm-signature";
const SCERET = "my-npm-webhook-secret";

app.use(
  bodyParser.json({
    verify: (req, _, buf) => (req.rawBody = buf),
  })
);

app.use("/api/npm-webhooks", (req, res) => {
  const reqHeaderSignature = req.headers[XNPMSignature];
  const signature = crypto
    .createHmac("sha256", SCERET)
    .update(req.rawBody)
    .digest("hex");

  if (reqHeaderSignature === "sha256=" + signature) {
    res.status(200).send("verifyed success!");
  } else {
    res.status(400).send("verifyed fail!");
  }
});

app.listen(PORT, () => {
  console.log("npm-webhook server is running on port 3000");
});
```

上述流程所使用的消息传输方式被称之为 —— HMAC (Hash Based Message Auth Code) 基于哈希的消息认证码技术。

简单来说，就是当订阅主题发生变更时 npm 会使用密钥对下发的请求内容进行“签名”，并将签名保存在 `x-npm-signatures` 的 Header 中一同下发。服务的订阅者在接收到请求时，基于相同的流程对请求的原始内容也使用密钥进行签名，最后将两个签名进行比对，如果与 `x-npm-signatures` 的签名相同，则身份认证通过且发送的消息内容没有被篡改。

如果需要进一步提高安全性，还可以从以下方法入手：
- IP 白名单控制：只允许接收特定范围 IP 地址的请求。
- 请求频率控制：设置时间窗口、最大请求数、惩罚时间等。
