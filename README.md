# Arch Linux Documentation

## Preface
> The purpose of this document is to install Arch Linux and understand how it works and what the commands do when building the system from scratch. I hope that this experience can benefit me in future and those who stumble upon it.

The Notes follows the format showcased in the [Arch Linux Installation Guide](https://wiki.archlinux.org/title/Installation_guide). This is not necessarily a guide, more like a reference for me and notes explaining things and how they work. There are some sections that I skip because the Arch Wiki explains it quite well.
## 1 Pre-Installation
---
### 1.2 Verify signature
When installing Arch it is recommended to use a torrent client to download the .iso file. Do not do this. It is much better to download it using one of the mirrors provided and verifying the signature "on a system with GnuPG installed".

```
gpg --keyserver-options auto-key-retrieve --verify archlinux-_version_-x86_64.iso.sig
```
This is literally the bare minimum, I would recommend going to the wiki and having a look at how to verify the authenticity of the file.

> [!CAUTION] Follow Wiki until section 1.4 Boot the live environment
### 1.4 Boot the live environment
- Secure Boot needs to disabled in the UEFI Firmware settings (BIOS) and can be enabled later when Arch is installed.
- EFI/UEFI needs to be enabled in the VirtualBox settings in order to follow the installation guide precisely
- To turn off the taskbar at the bottom of VirtualBox. User Interface > Mini Toolbar > Show in Full-screen/Seamless.

> [!CAUTION] Follow Wiki until section 1.9 Partition the disks
### 1.9 Partition the disks
#### To LVM or to not LVM?
In the example layouts section of the manual it states that "If you want to create any stacked block devices for [LVM](https://wiki.archlinux.org/title/Install_Arch_Linux_on_LVM "Install Arch Linux on LVM"), [system encryption](https://wiki.archlinux.org/title/Dm-crypt "Dm-crypt") or [RAID](https://wiki.archlinux.org/title/RAID "RAID"), do it now.". What are they and why would we want that? The only reason I ask this instead of following the normal installation route of assigning `Linux x86-64 root` to the root partition was because this [video](https://www.youtube.com/watch?v=FxeriGuJKTM) where Arch was installed differently, therefore I thought it is probably worth looking into.

> If you want to create any stacked block devices for [LVM](https://wiki.archlinux.org/title/Install_Arch_Linux_on_LVM "Install Arch Linux on LVM"), [system encryption](https://wiki.archlinux.org/title/Dm-crypt "Dm-crypt") or [RAID](https://wiki.archlinux.org/title/RAID "RAID"), do it now.

If you can make the partition for the root LVM, this makes it easier down the road and it allows for the creation of snapshots which is necessary for a rolling release distribution like arch.
- https://www.youtube.com/watch?v=xhVS1HKwGWw
	- Goes into how to make arch stable, one of the things mentioned is to make snapshots
- https://www.reddit.com/r/linux/comments/6w046b/to_lvm_or_not_to_lvm_that_is_the_question/
	- Peoples opinions on LVM, seems to be overall a good thing, especially with backups and VMs.
- https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)

>LVM or Logical Volume Manager is a alternative mechanism to manage storage systems using traditional partitioning. You can create logical volumes and mount them to your system as you would with a regular partition. (just don't use logical volumes for the boot partition).
>
>If you've ever had the displeasure of experiencing partition resizing then you'd wanna use LVM.
https://linuxhandbook.com/lvm-guide/
It's understandable why instead of `Linux x86-64 root` you would utilize `Linux LVM` instead for the root partition. because of this.
### Encrypting root partition

Encrypting the partition for security. This option is optional, but it's good if you are travelling or just want extra piece of mind. We encrypt the partition using `luksFormat` and then open the partition so that we can use it.

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

Installing kernel module.
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

## 2. Installation
---
```
pacstrap -K /mnt base linux linux-firmware
```

```
genfstab -U /mnt >> /mnt/etc/fstab
```

https://wiki.archlinux.org/title/Installation_guide#Configure_the_system