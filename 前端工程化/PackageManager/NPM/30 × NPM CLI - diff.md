## 对比包版本的差异

比较包不同版本的差异。该命令与 `git diff` 功能类似。

```shell
npm diff --diff date-toolkit@0.8.6 --diff date-toolkit@0.8.8
```

下面是两个版本比较的输出结果：
```shell
diff --git a/package.json b/package.json  # 对比的两个文件
index v0.8.6..v0.8.8 100644               # 对比文件的两个版本，最后面的 100644 是文件的权限。 
--- a/package.json                        # - 表示的是 a 文件，也是原始文件内容。
+++ b/package.json                        # + 表示的是 b 文件，也是新文件内容。
@@ -1,6 +1,6 @@                           # @@ 标识的是一个 Hunk 头，下面的内容均是该 Hunk 的内容。
 {
   "name": "date-toolkit",
-  "version": "0.8.6",                    # 原始文件内容。
+  "version": "0.8.8",                    # 新文件内容。
   "description": "The package used to learn the date time related calculation algorithm (Not guaranteed to be bug free)",
   "type": "module",
   "main": "index.js",
diff --git a/README.md b/README.md
index v0.8.6..v0.8.8 100644
--- a/README.md
+++ b/README.md
@@ -24,6 +24,7 @@
//**** 后续内容省略。
```

## Hunk 详解

Hunk 在编程领域下的含义是“大块”的意思，在 `diff` 命令的语境下则表示大块存在内容变化的的代码块。Hunk 头信息以 `@@` 开头与结尾。

在上面的例子中 `@@ -1,6 +1,6 @@` ，`-` 代表原始文件；`+` 代表新文件；`-1` 与 `+1` 分别表示 Hunk 内容的第一行分别位于两个文件中的那一行，而最后的 6 则表示 Hunk 内容的总行数。

现在分析 `@@ -24,6 +24,7 @@` Hunk 头信息，则表示 Hunk 内容是从两个文件的第 24 行开始，对于原始文件来说影响的内容范围有 6 行；而对新文件而言，Hunk 内容有 7 行；所以两者对比，新文件比原始文件增加了一行。

## Hunk 的差异上下文行数

如果感觉输出的 Hunk 内容上下文行数过多，则可以指定 `--diff-unified` 参数自定义上下文行数，其默认值为 3。

```bash
npm diff --diff date-toolkit --diff date-toolkit@0.8.6 --diff-unified 0
```

这样只会显示具有内容变化的行。
## --diff 详细用法

`--diff` 参数支持的取值类型与 `install` 相同，可以支持多种“包”的类型。
主要有以下几种情况：
- 取值为空时
- 取值为包名时
- 取值为语义化版本号时
- 取值为分发标签时
- 取值为指向包的 URL 时

[[前端工程化/PackageManager/NPM/assets/ac1edc332028a8d4ff8cf1756c09ff94_MD5.jpeg|Open: Pasted image 20240930114741.png]]
![[前端工程化/PackageManager/NPM/assets/ac1edc332028a8d4ff8cf1756c09ff94_MD5.jpeg]]

## 聚焦特定的文件或目录

为了更聚焦于特定变化的内容，你还可以通过 `glob` 模式过滤文件或目录

```bash
npm diff --diff date-toolkit --diff date-toolkit@0.8.6 ./README.md
npm diff --diff date-toolkit --diff date-toolkit@0.8.6 ./src/
```

执行上面命令只会输出 `README.md` 文件或 `src` 目录中文件的 Hunk 。


## 常用参数
| 参数名                 | 说明                             |
| ------------------- | ------------------------------ |
| `--diff-name-only`  | 仅显示文件名，而不显示 Hunk 内容。           |
| `--diff-unified`    | 自定义上下文行数。默认值 3                 |
| `--diff-no-prefix`  | 不显示用来区分原始文件（源文件）与新文件（目标文件）的前缀。 |
| `--diff-src-prefix` | 自定义源文件前缀。<br>默认 `a/` `b/` 区别。  |
| `--diff-dst-prefix` | 自定义目标文件前缀。<br>默认 `a/` `b/` 区别。 |
