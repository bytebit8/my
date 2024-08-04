## [什么是 AppImage？](https://doc.appimage.cn/docs/appimage/)
顾名思义，AppImage 就是应用程序镜像文件，其特性就是将应用程序本体以及相关依赖与库文件打包在一起，从而各个不同的 Linux 发行版中运行。其理念类似与 Window 平台免安装的 exe 文件，一个应用程序 = 一个文件。
AppImage 由 AppImageKit 工具包构建。
## 为什么使用 Obsidian.AppImage？
- [ ] snap 版本的 Obsidian 在 Ubuntu 系统中无法输入中文。
- [ ] 只有 `Obsidian.AppImage` 版本才有 git 同步功能
## 安装时可能会遇到那些问题？
1. 可能会缺少 `fuse` 依赖库。fuse 可以创建用户空间的文件系统，这是 AppImage 运行的基础。
在 Ubuntu 24+ ：
``` shell
sudo apt install libfuse2t64
```
## 安装 Obsidian.AppImage
### 下载 Obsidian-1.6.7.AppImage
[Obsidian-1.6.7-AppImage](https://github.com/obsidianmd/obsidian-releases/releases/download/v1.6.7/Obsidian-1.6.7.AppImage)
### 设置为可执行文件
1. 可以鼠标右键设置为可执行文件
2. 或者使用 `chmod` 命令。
``` shell
chmod u+x Obsidian-1.6.7-AppImage
```
### 提取 desktop 文件以及图标

```shell
./Obsidian-1.6.7-AppImage --appimage-extract

su # 切换管理员账户
mkdir /opt/obsidian
cp ./Obsidian-1.6.7.AppImage /opt/obsidian/obsidian
cp ./squashfs-root/obsidian.desktop /opt/obsidian/
cp ./squashfs-root/obsidian.png /opt/obsidian/
```
### 修改 desktop 文件
主要是修改 `Exec` 与 `Icon` 两处。
``` text
[Desktop Entry]
Name=Obsidian
Exec=/opt/obsidian/obsidian --no-sandbox %U
Terminal=false
Type=Application
Icon=/opt/obsidian/obsidian.png
StartupWMClass=obsidian
X-AppImage-Version=1.6.7
Comment=Obsidian
MimeType=x-scheme-handler/obsidian;
Categories=Office;
```