# My Personal Arch installation guide

This is a personal guide to install Arch linux with minimal Plasma DE. I must warn that this install is super minimal and does not contain many KDE apps like Kmail and Kalendar!

I am not a professional by any means and I made this quide just for personal use so I highly recommend you to read the official [`wiki`](https://wiki.archlinux.org/index.php/Installation_guide)!




# Begin to install base system

**Ensure the system clock is accurate**

```
timedatectl set-ntp true
```

To check the service status, use `timedatectl status`

**Load keys**

`localectl list-keymaps` <\-\- list all keymaps

`loadkeys keymap (keymap)` <--load keymap for current session

**Wifi** `(skip this if you use ethernet)`

Connect to WiFi using iwctl. Just enter this command and follow the instructions

```
iwctl
```

List all your wireless interfaces

```
device list
```

You need to select the preferred one.

Scan for available network using the command below:

```
station wlan0 scan
```

See the connections available

```
station wlan0 get-networks
```

Connect to your target Wi-Fi:

```
station wlan0 connect "Name of Network/WiFi"
```

**Test internet**

```
ping google.com -c 3
```

# Partitioning

**List your disks**

`fdisk -l` (will show all of your disks as "/dev/sda or /dev/nvme0n1 etc.")
![Screenshot_20220816_204521](https://user-images.githubusercontent.com/95308907/184945038-16875f8b-dd70-459e-91ee-82784ae5caa3.png)

**Now let's make root and EFI-partitions**
`cfdisk /dev/sda` or `cfdisk /dev/nvme0n1` (which shows above)

![Select-Arch-Linux-Installation-Disk](https://user-images.githubusercontent.com/95308907/184943576-cea39914-feac-4672-8f0e-3467130a27dd.png)

create at least 300M EFI system. Ppress enter key, select Type from the bottom menu and choose EFI System partition type, as shown in the following screenshots.

![EFI-System-Type](https://user-images.githubusercontent.com/95308907/184942340-c3c32914-614c-4c08-97bd-5d6dd4e77adb.png)


![Select-EFI-System](https://user-images.githubusercontent.com/95308907/184942636-c29c7650-8b30-4424-bda7-2fdd57efbe11.png)


**create root partition**

For /(root) partition use the following configuration: New -> Size: rest of free space -> Type Linux filesystem.

After you review Partition Table select Write, answer with yes in order to apply


**Format partitions**

```
mkfs.fat -F32 /dev/(efi partition)
```
```
mkfs.ext4 /dev/(root partion)
```

**Mounting partitions**

```
mount /dev/[root partition name] /mnt
mkdir -p /mnt/boot/efi
mount /dev/[efi partition name] /mnt/boot/efi
```

**Install base system**

```
pacstrap /mnt base base-devel linux linux-firmware linux-headers vim nano
```

**Make fstab**

```
genfstab -U /mnt >> /mnt/etc/fstab
```

# Enter your system

Enter chroot (arch installation)

```
arch-chroot /mnt
```

**Set keyboard language and time**

```
 localectl list-keymaps <-- list all keymaps
 loadkeys keymap (keymap) <--load keymap for current session
```

**set persistent keymap**

```
nano /etc/vconsole.conf
kaymap=(keymap)
```

**Set timezone**

to list available zones

```
timedatectl list-timezones
```

**set timezone**
Use you own Country/City!

```
ln -sf /usr/share/zoneinfo/Europe/Helsinki /etc/localtime
```

**Run hwclock to generate /etc/adjtime**

```
hwclock --systohc
```

**set ntp**
```
timedatectl set-ntp true
```

**Setting locale**

```
nano /etc/locale.gen
(Uncomment en_US.UTF-8 UTF-8)
```

**Run locale-gen**
```
locale-gen
```
**Set your locale to /etc/locale.conf**
```
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

**Set hostname**
```
echo "yourhostname" > /etc/hostname
```


**Set up host file (not necessary anymore if you don't need hostfile)**
```
touch /etc/hosts
```
```
127.0.0.1	localhost
::1		localhost
127.0.1.1	(myhostname)
```


**Install reguired packages**

```
pacman -S networkmanager network-manager-applet intel-ucode(or amd-ucode) wireless_tools grub efibootmgr bluez bluez-utils power-profiles-daemon
```

**Enable Network and bluetooth**

```
systemctl enable NetworkManager
systemctl enable bluetooth.service
```

**Set your user**

```
passwd <-- for root
useradd -m (username)
passwd (username)
usermod -a -G wheel (username)
EDITOR=nano visudo
(uncomment) ->  %wheel ALL=(ALL) ALL)
```

**Install GRUB**

```
grub-install --target=x86_64-efi --bootloader-id=GRUB --efi-directory=/boot/efi
```

**Save GRUB:**

```
grub-mkconfig -o /boot/grub/grub.cfg
```

Reboot and log in -->

# Plasma, Display, Sound

```
sudo pacman -S xf86-video-amdgpu (xf86-video-your gpu type, see the wiki)
```

**Install sound drivers**

```
sudo pacman -S pipewire pipewire-alsa pipewire-jack pipewire-pulse wireplumber gst-plugin-pipewire --needed
```

**Install Desktop Enverioment and Software( KDE Plasma)**

```
sudo pacman -S  plasma-meta dolphin ark konsole gwenview powerdevil ffmpegthumbs firefox kate spectacle --needed
```

**Enable sddm**

```
sudo systemctl enable sddm
```

**enable trim**

```
sudo systemctl enable fstrim.timer
```
# Bonus

**Swap file**

If you have less than 32 gigabytes of ram it is highly good idea to make swap file.
You can follow this guide to make your swap file: https://bogdancornianu.com/change-swap-size-in-ubuntu/

# 

exit

reboot

enjoy!
