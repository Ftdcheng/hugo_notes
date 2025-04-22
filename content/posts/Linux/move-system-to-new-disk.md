---
title: "旧磁盘数据迁移到新磁盘"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["Linux"]
---

# 旧磁盘数据迁移到新磁盘

## 安装硬盘

关闭主机，将新的磁盘安装到主机上，启动系统。数据的迁移需要借助操作系统，所以得确保磁盘在主机上。

## 查看旧磁盘上挂载了哪些文件系统
我们假设旧磁盘设备是/dev/sda，然后可以使用`lsblk -f`查看，输出样例如下：

```
loop0       squashfs                                                  0   100% /snap/bare/5
loop1       squashfs                                                  0   100% /snap/core20/2496
loop2       squashfs                                                  0   100% /snap/gnome-42-2204/202
loop3       squashfs                                                  0   100% /snap/snap-store/1113
loop4       squashfs                                                  0   100% /snap/gnome-42-2204/176
loop5       squashfs                                                  0   100% /snap/core22/1748
loop6       squashfs                                                  0   100% /snap/gtk-common-themes/1535
loop7       squashfs                                                  0   100% /snap/core22/1802
loop8       squashfs                                                  0   100% /snap/snap-store/1216
loop9       squashfs                                                  0   100% /snap/gnome-3-38-2004/143
loop10      squashfs                                                  0   100% /snap/gnome-3-38-2004/119
loop11      squashfs                                                  0   100% /snap/core20/2434
loop12      squashfs                                                  0   100% /snap/snapd/23545
loop13      squashfs                                                  0   100% /snap/snapd/23771
sda                                                                            
├─sda1      vfat           8BC3-AF3D                               1.9G     0% /boot/efi
├─sda2      swap           a7510130-5064-42a3-abd0-787e8a4f131e                [SWAP]
└─sda3      ext4           1f234375-7152-4e77-a210-b7573423c600  689.7G    19% /
sdb                                                                            
└─sdb1      ext4           691e6f03-2d31-4de8-ba6a-0754f2924dd6    2.4T    29% /home
sdc                                                                            
└─sdc1      ext4           2eb7de08-5701-4657-b69a-f0deab8803af    1.3T    60% /data
sdd         ext4           dd08c204-9596-44f3-b9be-35ef81650d2e      7T    30% /data1
sde         ext4           086d0c1f-5842-410e-b7d2-90af4b647f96    8.5T    16% /data2
...
```

这表明分区`/dev/sda1`挂了`/boot/efi`，`/dev/sd2`挂了交换分区，/dev/sda3挂的`/`。
此外`/home`和`/data`、`/data1`和`/data2`挂在其他磁盘上，等会复制根分区的时候，应该排除。

根据旧磁盘文件分区的大小比例分配新的分区大小。

- `/boot/efi`应该分500M ~ 1G
- `swap`分区分32G ~ 125G。如果没开休眠32G旧够了，开休眠应和内存大小一致，这里是125G。我们这里不开休眠，分32G
- `/`分区占剩下所有的空间

根据分区需求，使用 `parted` 对磁盘进行分区的步骤如下：

## 假设：
- 你的**新磁盘**是 `/dev/nvme0n1`，如果是其它磁盘请根据实际情况替换。
- 你已经知道分区需求：**1GB 的 EFI 分区**，**32GB 的 Swap 分区**，剩余空间作为根分区（`/`）。

---

## 一、使用 `parted` 创建分区

1. **启动 `parted`**：
   先启动 `parted` 并指定要操作的磁盘 `/dev/nvme0n1`（如果是其他磁盘，请替换为实际的设备名）：

   ```bash
   sudo parted /dev/nvme0n1
   ```

2. **设置磁盘分区表类型（GPT）**：
   如果是新磁盘，建议使用 GPT（适用于大于 2TB 的磁盘或 UEFI 系统）。设置分区表为 GPT：

   ```bash
   (parted) mklabel gpt
   ```

3. **创建 EFI 分区（1GB）**：
   创建一个 1GB 的 EFI 分区，类型为 FAT32，挂载点为 `/boot/efi`。

   ```bash
   (parted) mkpart primary fat32 1MiB 1GiB
   (parted) set 1 boot on  # 标记为启动分区
   ```

   - `1MiB` 是为了保证 GPT 的开头没有问题。
   - `1GiB` 是结束位置，表示分区大小为 1GB。

4. **创建 Swap 分区（32GB）**：
   创建一个 32GB 的 Swap 分区：

   ```bash
   (parted) mkpart primary linux-swap 1GiB 33GiB
   ```

   - 从 1GB 开始，到 33GB 结束，创建 32GB 的交换分区。

5. **创建根分区 `/`（剩余空间）**：
   使用剩余的空间来创建根分区 `/`，并选择一个合适的文件系统（如 ext4）。

   ```bash
   (parted) mkpart primary ext4 33GiB 100%
   ```

6. **查看分区情况**：
   查看当前分区表，确保分区创建无误：

   ```bash
   (parted) print
   ```

7. **退出 `parted`**：
   完成分区后，输入 `quit` 退出 `parted`：

   ```bash
   (parted) quit
   ```

---

## 二、格式化分区

分区完成后，需要对每个分区进行格式化：

1. **格式化 EFI 分区（FAT32）**：

   ```bash
   sudo mkfs.fat -F32 /dev/nvme0n1p1
   ```

2. **格式化 Swap 分区**：

   ```bash
   sudo mkswap /dev/nvme0n1p2
   sudo swapon /dev/nvme0n1p2
   ```

3. **格式化根分区 `/`（ext4）**：

   ```bash
   sudo mkfs.ext4 /dev/nvme0n1p3
   ```

---

## 三、挂载分区

1. **挂载根分区**：

   ```bash
   sudo mount /dev/nvme0n1p3 /mnt
   ```

2. **挂载 EFI 分区**：

   ```bash
   sudo mkdir /mnt/boot
   sudo mkdir /mnt/boot/efi
   sudo mount /dev/nvme0n1p1 /mnt/boot/efi
   ```

3. **启用 Swap 分区**：

   ```bash
   sudo swapon /dev/nvme0n1p2
   ```

---
## 四、复制根分区（`/`）数据
将原根分区的数据复制到新硬盘的根分区。假设原根分区是 /dev/sda3，使用 rsync 来复制数据。不过根目录的下的所有目录不是都要复制的，我们要排除一些目录，比如home之类的，我们使用rsync完成复制，--exclude选项排除目录：
```bash
sudo rsync -aAXHv \
           --exclude=/dev/* \
           --exclude=/proc/* \
           --exclude=/sys/* \
           --exclude=/tmp/* \
           --exclude=/run/* \
           --exclude=/mnt/* \
           --exclude=/media/* \
           --exclude=/swapfile \
           --exclude=/boot/efi \
           --exclude=/data* \
           --exclude=/home/* \
           --exclude=/media/* \
           / /mnt
```
---
## 五、复制 EFI 分区内容
将原 EFI 分区的内容复制到新 EFI 分区。假设原 EFI 分区是 /dev/sda1：
```bash
sudo mount /dev/sda1 /mnt/old_efi
sudo rsync -av /mnt/old_efi/ /mnt/boot/efi/
```
---
## 六、更新 `/etc/fstab`

为了确保系统启动时自动挂载这些分区，需要编辑 `/etc/fstab` 文件。首先获取每个分区的 UUID：

```bash
sudo blkid
```

找到相应的分区 UUID，例如：

```
/dev/nvme0n1p3: UUID="cbe03289-0caf-40eb-965d-03c10ca7bf1b" TYPE="ext4" PARTLABEL="primary" PARTUUID="53b551f1-e4d6-4832-9c8d-7491c095e523"
/dev/nvme0n1p1: UUID="94BA-6041" TYPE="vfat" PARTLABEL="primary" PARTUUID="f71de349-8ffa-4d5c-ba47-f9597fecb777"
/dev/nvme0n1p2: UUID="9ef999f2-7a17-48dd-85d0-bd7b58975327" TYPE="swap" PARTLABEL="primary" PARTUUID="f8a1397f-1ea0-404b-88ff-42fc0b00557d"
/dev/sda1: UUID="8BC3-AF3D" TYPE="vfat" PARTUUID="4517423a-3cd1-4cd4-abb5-f36ebb37701d"
/dev/sda3: UUID="1f234375-7152-4e77-a210-b7573423c600" TYPE="ext4" PARTUUID="f1671f3b-34c1-48f5-9cd0-58d8e3f6c382"
/dev/sdb1: UUID="691e6f03-2d31-4de8-ba6a-0754f2924dd6" TYPE="ext4" PARTUUID="ba8af566-9f5d-415b-83de-6f7c428e8ceb"
/dev/sdc1: UUID="2eb7de08-5701-4657-b69a-f0deab8803af" TYPE="ext4" PARTUUID="df23adf3-3e53-41dc-9e89-289bf861a8ab"
/dev/sdd: UUID="dd08c204-9596-44f3-b9be-35ef81650d2e" TYPE="ext4"
/dev/sde: UUID="086d0c1f-5842-410e-b7d2-90af4b647f96" TYPE="ext4"

```

然后编辑 `/etc/fstab`，添加以下内容：

```bash
# / was on /dev/nvme0n1p3 during installation
UUID=cbe03289-0caf-40eb-965d-03c10ca7bf1b /               ext4  errors=remount-ro 0       1
# swap was on /dev/nvme0n1p2 during installation
UUID=9ef999f2-7a17-48dd-85d0-bd7b58975327 none            swap    sw              0       0
# /boot/efi was on /dev/nvme0n1p1 during installation
UUID=94BA-6041                            /boot/efi       vfat    umask=0077      0       1
```

确保根分区 `/` 的挂载顺序为 1，因为它是根分区。

---
## 七、更新引导加载器（GRUB）
由于你更换了硬盘，你需要重新安装 GRUB 引导加载器，以确保系统能够正确引导。
1. 绑定系统目录以便操作：

```bash
sudo mount --bind /dev /mnt/dev
sudo mount --bind /proc /mnt/proc
sudo mount --bind /sys /mnt/sys
sudo mount --bind /run /mnt/run
```
2. 安装和更新 GRUB：
```bash
sudo chroot /mnt
grub-install /dev/nvme0n1
update-grub
```
3. 退出 chroot 环境：

```bash
exit
```
---
## 八、卸载分区并重启
```bash
sudo umount /mnt/boot/efi
sudo umount /mnt
sudo swapoff /dev/nvme0n1p2
```

## 九、检查启动

在重新启动时，确保系统从新的 NVMe 硬盘启动，检查是否能够正常进入系统。如果一切顺利，系统将从新的硬盘启动，使用新的 EFI 分区、Swap 分区和根分区。