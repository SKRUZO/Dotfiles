# Process Steps
A list of steps for installing Arch Linux. Do not use this list as an installation guide, as Arch is a rolling release OS and constantly changing. This is just for my sake, as I've had to re-install Arch like 4 times in the last day and sometimes the steps are a little tricky.

## Before
1. Download the .ISO image from [Archlinux.org](https://www.archlinux.org/download/ "ArchLinux.org Download Page").
2. Burn the .ISO to a flash drive using [Rufus](http://rufus.akeo.ie/)
3. Ensure a wired Ethernet connection is available, for simplicity.
4. Give yourself some time to do this.

## Installing
1. Boot with flash drive inserted. Boot to the flash drive.
2. `fdisk -l` to list storage devices. If an OS is present, partitions can be seen here.
3. `cfdisk` to open the partition manager. Delete old partitions and write the primary partition for storage. Set sda1 to bootable. Create a second partition for SWAP, around 2x the size of your RAM. Change its type to Linux Swap. Write partitions to disk, and exit cfdisk.
4. `mkfs.ext4 /dev/sda1` to create the file system, use `mkswap /dev/sda2` to make the SWAP partition, then mount the main drive with `mount /dev/sda1 /mnt` and turn on the SWAP with `swapon /dev/sda2`
5. `pacstrap /mnt` to download the base packages. Append this command with desire packages including but not limited to: `base base-devel grub xorg-server xorg-xinit xorg-apps alsa-utils vim git`
6. `arch-chroot /mnt` to enter the mount point.
  1. `passwd` to set a password for the root user.
  2. `vim /etc/locale.gen`, Comment out needed locales in  using editor of choice, then run `locale-gen`
  3. `ln -sf /usr/share/zoneinfo/US/Pacific /etc/localtime` to set localtime to Pacific. Change as needed.
  4. `echo HOSTNAME > /etc/hostname` Set hostname.
  5. `useradd -m -g users -G wheel -s /bin/bash USERNAME` Create user account. Set password using `passwd USERNAME`
  6. `vim /etc/sudoers` Add wheel to list of sudoers.
  7. `grub-install /dev/sda` Install grub
  8. `mkinitcpio -p linux` to generate initialize.
  9. `grub-mkconfing -g /boot/grub/grub.cfg` Make grub config file
  10. `exit` the chroot shell.
7. `genfstab -p /mnt >> /mnt/etc/fstab` Generate filesystem table
8. `umount -R /mnt` Unmount
9. `shutdown` the system, remove flash drive, and reboot.

## Initial Useage
1. Log in as created user.
2. `sudo cp /etc/netctl/examples/ethernet-dhcp /etc/netctl/` to copy DHCP Ethernet profile to Netctl.
3. Use `ip a` to list internet devices, and note the primary connection device (something like enp0s3)
4. `vim /etc/netctl/ethernet-dhcp` to write device to profile.
5. `sudo netctl start ethernet-dhcp` to start this profile. Follow with `sudo netctl enable ethernet-dhcp` to permanently enable this profile.
6. `sudo vim /etc/pacman.conf` and append the following
```
[archlinuxfr]
SigLevel = Never
Server = http://repo.archlinux.fr/$arch
```
7. `sudo pacman -Syu yaourt` to synchronize packages and update, as well as install Yaourt.
8. Install window manager of choice. In my case, `yaourt i3-gaps`
9. Install other cursory programs as needed. Thinks like `firefox i3lock dmenu rofi htop ranger compton i3blocks-gaps-git `
10. If desired, copy over dotfiles, fonts, scripts, and images from GitHub repo.
