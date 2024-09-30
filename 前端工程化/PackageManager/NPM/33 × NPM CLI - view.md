查看包在注册表中的信息

## 常用别名

```shell
npm info vue
npm show vue
npm v vue
```

## 查看包的基本信息

```shell
npm view vue
```

## 查看包的所有信息

以 JSON 格式查看包在注册表中的所有信息。
```shell
npm view vue --json
```

## 查看包的分发标签

```shell
➜  ~ npm view vue dist-tags
{
  csp: '1.0.28-csp',
  legacy: '2.7.16',
  'v2-latest': '2.7.16',
  alpha: '3.5.0-alpha.5',
  beta: '3.5.0-beta.3',
  rc: '3.5.0-rc.1',
  latest: '3.5.10'
}
```

## 查看包的所有版本号

```shell
 npm view vue versions
```

## 查看包的版本号与对应发布时间

```shell
npm view vue time
```

## 查看包的依赖信息

```shell
npm view vue dependencies
npm view vue devDependencies
npm view vue peerDependencies
npm view vue optinalDependencies
```
