## 添加 Windows 启动项

向 `/etc/grub.d/11_custom` 中追加以下代码：
```grub
menuentry "Windows" --class windows {
    insmod part_gpt
    insmod fat
    insmod search_fs_uuid
    insmod chain
    search --fs-uuid --no-floppy --set=root 5A20-8FB4
    chainloader (${root})/EFI/Microsoft/Boot/bootmgfw.efi
}
```

* `--class windows` 主要是用于表示引导的系统类别。
* UEFI 引导使用 `part_gpt`; BIOS 引导使用 `part_msdos`。
* UEFI 引导分区的文件系统类型通常是 `fat`。
* `5A20-8FB4` 是 EFI 分区的 UUID。
* `/EFI/Microsoft/Boot/bootmgfw.efi` ： 引导项的 efi 文件在 EFI 分区中的路径。不同的系统并不相同。

## 查看分区类型与 UUID

使用 `blkid` 命令：
```text
/dev/nvme0n1p1: UUID="5A20-8FB4" BLOCK_SIZE="512" TYPE="vfat" PARTLABEL="EFI System Partition" PARTUUID="a40e6935-8c37-4744-8ff3-3307fb5261f7"

/dev/sda2: UUID="949C-4064" BLOCK_SIZE="512" TYPE="vfat" PARTUUID="865a525b-819a-41a6-aaac-546484be3d39"
```
上面是分别位于两块硬盘的两个 EFI 分区。
如果发行版具有磁盘工具，也可以使用磁盘工具查看。

## 启动项顺序控制

文件名的前缀数字用于控制 `update-grub` 命令读取并执行文件执行的顺序，也最终影响启动菜单的显示顺序。