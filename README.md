# Arch Linux Documentation
## Preface
> The purpose of this document is to install Arch Linux and understand how it works and what the commands do when building the system from the ground up. I hope that this experience can benefit me in future and those who stumble upon it.

The Notes follows the format showcased in the [Arch Linux Installation Guide](https://wiki.archlinux.org/title/Installation_guide). This is not necessarily a guide, more like a reference for me and notes explaining things and how they work. There are some sections that I skip because the Arch Wiki explains it quite well.
## 1 Pre-Installation
### 1.2 Verify signature
When installing Arch it is recommended to use a torrent client to download the .iso file. Do not do this. It is much better to download it using one of the mirrors provided and verifying the signature "on a system with GnuPG installed".

```
gpg --keyserver-options auto-key-retrieve --verify archlinux-_version_-x86_64.iso.sig
```
This is literally the bare minimum, I would recommend going to the wiki and having a look at how to verify the authenticity of the file.

> [!CAUTION] 
> Follow Wiki until section 1.4 Boot the live environment

### 1.4 Boot the live environment
- Secure Boot needs to disabled in the UEFI Firmware settings (BIOS) and can be enabled later when Arch is installed.
- EFI/UEFI needs to be enabled in the VirtualBox settings in order to follow the installation guide precisely
- To turn off the taskbar at the bottom of VirtualBox. User Interface > Mini Toolbar > Show in Full-screen/Seamless.

> [!CAUTION] 
> Follow Wiki until section 1.8 Partition the disks (If you are following with the wiki)
### 1.6 Verify Boot Mode
I like verifying the boot mode because it makes me feel cool, so I'll put the command here
```
cat /sys/firmware/efi/fw_platform_size
```
### 1.8 Update the system clock
You can check if the system clock is synced using the command below
```
timedatectl
```
You can change the timezone using the command below
```
timedatectl list-timezones
timedatectl set-timezone <timezone>
```
### 1.9 Partition the disks
#### 1.9.1 Encrypting Drives with Random data.
Good practice to override your disk with random data to hide your data boundary. You can write random data by using the `dd` command.

```
dd if=/dev/urandom of/dev/sda bs=4096 status=progress
```

This can take a REALLY LONG TIME. So much so that I don't even want to think about what happens if you screw the rest of the installation up. I only recommend this step if you know what you are doing.

https://youtu.be/YC7NMbl4goo
#### 1.9.1 Using fdisk
use `fdisk -l` to see available drives and use the command `fdisk /dev/drive_you_want_to_partition` to select it. You will then enter fdisk where you can edit and create partitions. Here are some useful commands.

| command | description                                                                           |
| ------- | ------------------------------------------------------------------------------------- |
| m       | help                                                                                  |
| g       | create a new empty GPT partition table, use this to get started formatting the drive. |
| n       | add new partition                                                                     |
| d       | delete partition                                                                      |
| t       | change the partition type, make sure you do this to all Partitions.                   |
| p       | print the partition table (to verify)                                                 |

I want to enable **Hibernation** on my system, that typically means RAM + 2gb. If you are following on virtual box just say like 4GB.
##### Single Storage Device Partition Table
| Mount point on the installed system | Partition                   | Partition type       | Suggested size                                            |
| ----------------------------------- | --------------------------- | -------------------- | --------------------------------------------------------- |
| `/boot`                             | `/dev/efi_system_partition` | EFI system partition | 1 GiB                                                     |
| `[SWAP]`                            | `/dev/swap_partition`       | Linux swap           | RAM + 2GiB (for hibernation) / 2GiB (without hibernation) |
| `/`                                 | `dev/root_partition`        | Linux LVM            | remainder of the device                                   |

##### Multiple Storage Device Partition Table
| Mount point on the installed system | Partition                   | Partition type       | Suggested size                                            |
| ----------------------------------- | --------------------------- | -------------------- | --------------------------------------------------------- |
| `/boot`                             | `/dev/efi_system_partition` | EFI system partition | 1 GiB                                                     |
| `[SWAP]`                            | `/dev/swap_partition`       | Linux swap           | RAM + 2GiB (for hibernation) / 2GiB (without hibernation) |
| `/`                                 | `dev/root_partition`        | Linux LVM            | remainder of the device                                   |

| Mount point on the installed system | Partition                   | Partition type       | Suggested size                                            |
| ----------------------------------- | --------------------------- | -------------------- | --------------------------------------------------------- |
| `/boot`                             | `/dev/efi_system_partition` | EFI system partition | 1 GiB                                                     |
| `[SWAP]`                            | `/dev/swap_partition`       | Linux swap           | RAM + 2GiB (for hibernation) / 2GiB (without hibernation) |
| `/`                                 | `dev/root_partition`        | Linux LVM            | remainder of the device                                   |

#### 1.9.2 To LVM or to not LVM?
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
>https://linuxhandbook.com/lvm-guide/

| Volume          | Description                                                                                          |
| --------------- | ---------------------------------------------------------------------------------------------------- |
| Physical Volume | The actual storage device that you want LVM to be initalized on                                      |
| Volume Group    | It is what we create when we combine multiple physical volumes to create a single storage structure. |
| Logical Volume  | A Partition.                                                                                         |
It's understandable why instead of `Linux x86-64 root` you would utilize `Linux LVM` instead for the root partition. because of this. It seems to make life more easier and it might allow for more storage especially if you have multiple hard drives without needing to mount?

A good resource on reading more about the subject is [Red Hat Documentation](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/8/html/configuring_and_managing_logical_volumes/index)
#### 1.9.3 Encrypting root partition

Encrypting the partition for security. This option is optional, but it's good if you are travelling or just want extra piece of mind. We encrypt the partition using `luksFormat` and then open the partition so that we can use it.

**Read More about LUKS [here](https://en.wikipedia.org/wiki/Linux_Unified_Key_Setup)**. It was created in 2004 and is originally intended for Linux. LUKS basically ensures that programs/operating systems all implement password management in a secure and documented manner. You can use this disk encryption specification to also encrypt your swap partition as well. However, re-encrypting swap also forbids using a [suspend-to-disk](https://en.wikipedia.org/wiki/Hibernation_(computing)) feature generally. 

We can encrypt the root partition, we can do this by using `cryptsetup`. The first command encrypts the partition with a passphrase or password. The second command unlocks the partition and gives it a name `lvm`. You can access this unlocked device from `dev/mapper/lvm` (this is done later)

```
cryptsetup luksFormat /dev/root_partition
crpytsetup open --type luks /dev/root_partition lvm
```

Create a physical volume that directs to the LUKS encrypted partition that we created. We also create a volume group using the physical volume created just now.

```
pvcreate /dev/mapper/lvm
vgcreate volgroup0 /dev/mapper/lvm
```

We can partition the voroup into `lv_root` and `lv_home` which are the names of our logical volumes. `-L` denotes the size of our logical volume and volgroup0 is the name of the volume group we are using to define our logical volumes.
```
lvcreate -L 30GB volgroup0 -n lv_root
lvcreate -l +100%FREE volgroup0 -n lv_home
lvdisplay
```

- **IF YOU WROTE THE WRONG SIZE YOU CAN USE** `lvextend -L +<size> /dev/<vg_name>/<lv_name>`
- **YOU CAN MAKE THE LOGICAL VOLUME TAKE UP THE REST OF THE AVAILABLE SPACE USING** `+100%FREE` **AS THE ARGUMENT TO `-l`**
	- no you are not mistaken `-l` is different to `-L`
	- You can leave extra room if you like

loading kernel module for managing LVM devices. This Kernel module is assumed to not be activated right out of the box. We then scan the virtual group to make sure they exist and apply them to the internal database since it is new and then activate all volume groups. 
```
modprobe dm_mod
vgscan
vgchange -ay
```

formatting logical home and root volumes to use ext4. The ext4 file system increases access speed and supports large file systems (which is what we have/could have due to enabling LVM on our root partition).
```
mkfs.ext4 /dev/volgroup0/lv_root
mkfs.ext4 /dev/volgroup0/lv_home
mkswap /dev/_swap_partition
mkfs.fat -F 32 /dev/_efi_system_partition_
```

Mount partitions and logical volumes to the machine.
```
mount /dev/volgroup0/lv_root /mnt
swapon /dev/swap_partition
mount --mkdir /dev/efi_system_partition /mnt/boot
mount --mkdir /dev/volgroup0/lv_home /mnt/home
```
## 2. Installation
Install required packages.
- `base` installs essential utilities for the system
- `linux` installs the latest stable Linux Kernel
- `linux-lts` install the long-term support kernel (which is nice to have in case the latest kernel breaks)
- `linux-firmware` Firmware for various devices
- `linux-headers` and `linux-lts-headers` header files define interfaces to functions and gives structures for programs to use. 
Initialize an empty pacman keyring in the target using the `-K` flag.

```
pacstrap -K /mnt base linux linux-firmware linux-headers linux-lts linux-lts-headers
```
### 3. Configuring the System
Critical for mounting partitions at boot time.
https://www.redhat.com/en/blog/etc-fstab
`fstab` or Linux filesystem table is a old school piece of technology that we simply cannot live without. Basically `fstab` allows users to define a specific set of rules whenever a file system is introduced to the system. Take a USB for example, when you plug it into your computer you don't really think about the mechanisms behind how it got mounted. without `fstab` you would have to mount it manually with the `mount` command.

```
genfstab -U /mnt >> /mnt/etc/fstab
```
https://wiki.archlinux.org/title/Installation_guide#Configure_the_system
Change the root to the new system (which is presumably installed/mounted onto your actual system at this point.). This creates a restricted environment where the process can only see and access files within the specified directory.

```
arch-chroot /mnt
```

#### Setting the Password
Writing `passwd` by itself assumes you want to change the password for the root user
```
passwd
```
#### Create a User
You can create a user using the `useradd` command.
 - `-m` creates a home directory for the user
 - `-g users` sets the primary group to `users`
 - `-G wheel` adds it to the administrative group
 - then the name of the user
```
useradd -m -g users -G wheel shirotohu
```
After that you can configure the password for this user.
```
passwd shirotohu
```
#### Installing other packages
```
pacman -S base-devel dosfstools grub efibootmgr lvm2 mtools networkmanager os-prober sudo
```
We install a bunch of stuff here, things to note here are lvm2, grub, networkmanager, and sudo.
> [!NOTE]
> note that we don't install a desktop environment here. We want to install KDE and Wayland for our installation and that part is left later.
#### Installing GPU Drivers
list your PCI devices and to find out the brand of GPU. We have a NVIDIA GPU sadly.
```
lspci
```
Install the drivers as such.
```
pacman -S nvidia nvidia-utils nvidia-lts
```
#### Generating RAM disks for our Kernel(s)
[mkinitcpio](https://gitlab.archlinux.org/archlinux/mkinitcpio/mkinitcpio) is a Bash script used to create an [initial ramdisk](https://en.wikipedia.org/wiki/Initial_ramdisk "wikipedia:Initial ramdisk") environment.
```
vim /etc/mkinitcpio.conf
```
go to the hooks line and add encrypt and lvm2 between filesystems and block
```
HOOKS=(base udev autodetect modconf kms keyboard keymap consolefont block encrypt lvm2 filesystems fsck)
```
make sure you see encrypt and LVM 2 in the output of the command below. You need to run the command for every kernel you have.
```
mkinitcpio -p linux
mkinitcpio -p linux-lts
```
#### Configuring the locale
uncomment your locale and then generate that locale.
```
vim /etc/locale.gen
locale-gen
```
#### Configuring GRUB
```
vim /etc/default/grub
```
Get the UUID of the encrypted drive using 
```
blkid <encrypted_device>
```
change the GRUB_CMDLINE_LINUX_DEFAULT to
```
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 cryptdevice=UUID=<device_uuid>:volgroup0 quiet"
```

#### Install GRUB
```
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=grub_uefi --recheck 
cp /usr/share/locale/en\@quot/LC_MESSAGES/grub.mo /boot/grub/locale/en.mo
grub-mkconfig -o /boot/grub/grub.cfg
systemctl enable NetworkManager
```
### Final Touches
exit the chroot environment and then
```
umount -a
reboot
```
none of this probably works and I will probably have to fix it.