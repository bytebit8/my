
管理 NPM 的配置参数。
```shell
npm config set key value       # 设置配置参数及值。
npm config get key             # 获取指定配置的值。
npm config delete key          # 删除指定的配置。
npm config list                # 列出当前所有的配置参数（默认为 User Config）
npm config edit                # 用命令行编辑器编辑配置文件。
```

## 列出当前的配置参数

```shell
npm config ls
```

## 查看全部默认的配置参数

```shell
npm config ls -l
```

## 编辑全局配置文件

```shell
npm config edit -L global
```

## 编辑用户配置文件

```shell
npm config edit -L user
```

## 编辑本地配置文件

```shell
npm config edit -L project
```

## JSON 格式输出

为了更好的查看配置结果，可以使用 `--JSON` 格式查看。
```bash
npm config list -l --jsonp
```

