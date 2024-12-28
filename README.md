# Arch Linux Documentation

## 1. Pre-Installation
---
### To LVM or to not LVM?

If you can make the parition for the root LVM, this makes it easier down the road and it allows for the creation of snapshots which is necessary with a rolling release distribution like arch.

https://www.reddit.com/r/linux/comments/6w046b/to_lvm_or_not_to_lvm_that_is_the_question/
https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)

> If you want to create any stacked block devices for LVM, system encryption or RAID, do it now.

### Encrypting root partition

Encrypting the partition for security. This option is optional, but it's good if you are travelling or just want extra piece of mind. We encrypt the partition using luksFormat and then open the partition so that we can use it.

```
cryptsetup luksFormat /dev/root_partition
crpytsetup open --type luks /dev/root_partition lvm
```

From there we create a physical volume. A volume that we will be using with LVM. we named it lvm in the command above and then it is referred to here. You also need to create a volume group.

```
pvcreate /dev/mapper/lvm
vgcreate volgroup0 /dev/mapper/lvm
```

logical volume for the root and home.
```
lvcreate -L 30GB volgroup0 -n lv_root
lvcreate -L 250GB volgroup0 -n lv_home
lvdisplay
```

Installing kernal module.
```
modprobe dm_mod
vgscan
vgchange -ay
```

formatting root file system.
```
mkfs.ext4 /dev/volgroup0/lv_root
mkfs.ext4 /dev/volgroup0/lv_home
```

Mounting file systems
```
mount /dev/volgroup0/lv_root /mnt
mount --mkdir /dev/efi_system_partition /mnt/boot
swapon /dev/swap_partition
mount --mkdir /dev/volgroup0/lv_home /mnt/home
```



