## grub 是什么？
grub 是 Linux 发行版中最常见的系统引导程序(bootloader)，负责对系统内核的加载。

## grub 配置文件
grub 的配置文件有两个：
* `/etc/default` ：grub 静态配置文件模板。
* `/boot/grub/grub.cfg` ：由静态配置模板文件生成的，最终由 grub 程序读取执行的配置文件。
## 修改字体
1. 使用 `grub-mkfont` 命令从 ttf 字体文件中生成可以被 grub 读取的 pf 字体文件。
```shell
grub-mkfont -s 22 -o /home/shen/UbuntuMono-R.ttf /boot/grub/ubuntu-mono.pf2
```
2. 编辑 grub 静态配置文件
```shell
sudo vim /etc/default/grub
```
加入以下内容：
```text
GRUB_FONT=/boot/grub/ubuntu-mono.pf2
GRUB_GFXMODE=1600x1050
```
3. 最后执行 `update-grub` 命令更新 `grub.cfg` 文件。
```shell
update-grub
```
## 使用 Grub Customizer

`Grub Customizer` 以图形可视化的方式编辑 grub 配置文件。
[How To Install Grub Customizer On Ubuntu](https://itsfoss.com/install-grub-customizer-ubuntu/)