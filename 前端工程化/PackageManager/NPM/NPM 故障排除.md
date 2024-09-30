## 常规

- 一些奇怪的问题可以通过简单地运行 `npm cache clean` 并重试来解决。
- 如果您在使用 npm CLI 命令时遇到问题，请使用 `—verbose` 或 `--log-leve verbose` 选项查看更多详细的日志信息。

## 日志权限不足
```bash
sudo chown -R $(whoami) /Users/xxx/.npm
sudo chmod -R u+w /Users/xxx/.npm