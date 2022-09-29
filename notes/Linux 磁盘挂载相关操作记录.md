---
title: Linux 磁盘挂载相关操作记录
tags: [Linux]
created: 2022-09-29T09:43:55.247Z
modified: 2022-09-29T09:58:30.719Z
---

# Linux 磁盘挂载相关操作记录

## Linux 重新挂载 HOME 目录

```shell
sudo mkfs.ext4 /dev/sdb
mkdir /mnt/home
sudo mount /dev/sdb  /mnt/home/
sudo rsync -aXS /home/. /mnt/home/.
sudo mv /home/ /home_bak
sudo mkdir /home
sudo umount /dev/sdb
sudo mount /dev/sdb /home

echo "UUID=$(blkid /dev/sdb -o value | head -n 1) /home /ext4 defaults 0 1" | sudo tee -a /etc/fstab
```
