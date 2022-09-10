## 1. Pre-installation
- #### Loading of keyboard layout 
     To see all the available keyboards layouts I type:
    ```
    ls /usr/share/kbd/keymaps/**/*.map.gz
    ```
    
- #### Set console keyboard layout
     To set the keyboard layout I type `loadkeys it` because i use italian layout.
     
- #### Connecting to the internet
     To list and ensure if network interfaces are enabled, I use the command:
     ```
     ip link
     ```
    - If I connect through ethernet, I just plug in the cable.
    - If I connect through Wi-Fi, i use `iwctl` utility to authenticate to the wireless connection. See the [iwctl](https://wiki.archlinux.org/title/Iwd#iwctl) documentation for more details.
    - Next i configure my network connection using `dhcpcd` client.
    - I verify the connection with `ping`:
      ```
      ping archlinux.org
      ```
- #### Updating the system clock
     I enable enable time synchronization services with:
     ```
     timedatectl set-ntp true
     ```
     
- #### Partitioning the disks (On a empty disk)
     I list the block device of disks using
     ```
     fdisk -l
     ```
     A block device of a disk is identified by `/dev/sda`, `/dev/nvme0n1` and so on...
    
    Now I have to choose which disk I'm going to use to install Arch Linux.
     In my case is `/dev/sda`.
     
     - In `/dev/sda` I create a `root` partition `/`, an `EFI` system partition for booting in UEFI mode and a          `swap` partition.
     
     
    To modify partition tables I use `cfdisk /dev/disk_to_be_partitioned`:
    ```
    cfdisk /dev/sda
    ```
    - First I create a GPT partition table selecting it from the initial menu.
    - Once GPT partition table is created, I make an `EFI` partition with `512 MB` of size. Then I select `'EFI         System'`from the `'Type'` menu.
    - Next I create the `swap` partition with `4 GB` of size. I assign `'Linux swap'` from the `'Type'` menu.
    - The remaining space is reserved for the `root` partition.
    - The correspodents new created partition in my case will be:
      -  `/dev/sda1` for EFI partition
      -  `/dev/sda2` for Swap partition
      -  `/dev/sda3` for root partition
    
-  #### Formatting the partitions
   Every created partition must be formatted with the appropriate file system with the following commands:
   
   - for the ext4 file system on root partition
   ```
   mkfs.ext4 /dev/sda3
   ```
   - for the EFI system partition
   ```
   mkfs.fat -F32 /dev/sda1 
   ```
   - for the swap file system partition
   ```
   mkswap /dev/sda2
   ```
   - #### Mounting the file system
   To mount all the created partitions I use the following commands:
   - to mount the root volume on `/mnt`
   ```
   mount /dev/sda3 /mnt
   ```
   - to mount the EFI partition i first create mount point with
   ```
   mkdir -p /mnt/boot/efi
   ```
     and then i mount the EFI partition with 
     ```
     mount /dev/sda1 /mnt/boot/efi
     ```
   - to enable swap volume with
   ```
   swapon /dev/sda2 
   ```
   
 ## 2. Installation
 - #### Selecting the mirrors
     Mirror servers are defined in `/etc/pacman.d/mirrorlist` and i use [reflector](https://wiki.archlinux.org/title/Reflector) to update my mirrors.  Somtimes i edit the file manually.  
 - #### Installing essential packages
     To install the `base` packages, the Linux `kernel` and `firmware` I use
     ```
     pacstrap /mnt base base-devel linux linux-firmware nano
     ```
     `Nano` is not required but i prefer installing it now to edit some text files later.
     I install other packages through `pacman` once I'm `chrooted` into the new system. 
