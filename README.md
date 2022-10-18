# Build-your-own-os
Linux from scratch (Linux 5.19.2-lfs-11.2-rc1)


You are recommended to go through https://www.linuxfromscratch.org/lfs/view/11.2-rc1/. By own.

if you want to build from the middle as the compiling process takes so long.
 Then, here are some patches pre-compiled from where you can install the kernel as per your choice and your system architecture.
 
The below-named tar file contains all the necessary packages with the latest GCC 11+, Linux 5.19.2-lfs-11.2-rc1

upto_before_8.82_stripping_-11.2-rc1.tar.xz 


# Download link .

https://drive.google.com/file/d/1Z7G1QxDR_kjUPTcWG5YfwythVx0-hRyv/view?usp=sharing





After extracting it as 

    tar -xpf $HOME/lfs-temp-tools-11.2-rc1.tar.xz

You can continue to chroot its environment, to know how to let's go through this link: https://www.linuxfromscratch.org/lfs/view/11.2-rc1/chapter07/introduction.html

You are not recommended to go through the process by copy paste as mine below but analyze the Documentation for LFS then 
It looks like this to me 
          
 Warning  ...
Users are recommended to read https://www.linuxfromscratch.org/lfs/view/development/chapter07/introduction.html first 
before doing the below. A small problem in choosing a drive might lead to losing valuable data.

For better, you can use one Pendvie around 4 GB. 

Open terminal in linux system 
Accessing sudo power

      sudo -i

First, run as root.Then only copy below ...

See man documentation for lsblk if you don't know man lsblk , for short it displays all the disks with partition available and their mount points

      lsblk
For comfort, the below is written so it's easy to identify the disk bcoz one wrong choice may be held to a crash system or loss of data.

       echo " \033[34m Please identify your disk as per their space and nb of partitions used ..\n Use the disk or partition where LFS tar was        extracted."
       echo" \n\n Give your disk identification /dev/sd?? to be mounted and which contains LFS Project \n \033[37m Eg: sdb3 or sdc1 or sdd4 \033[0m "
       read a
       
This may result in an error as not found or something in some system due to "read a" ignore it

       mkdir -pv /mnt/lfs
       mount /dev/$a /mnt/lfs
       print $a
       export LFS=/mnt/lfs
       
 Only one-time commands you don't have to do this again and again when you enter to chroot environment.
 
      chown -R root:root $LFS/{usr,lib,var,etc,bin,sbin}
      case $(uname -m) in
        x86_64) chown -R root:root $LFS/lib64 ;;
      esac
This will mount or bind your current host system elements such as below.
      mkdir -pv $LFS/{dev,proc,sys,run}
      mount -v --bind /dev $LFS/dev
      mount -v --bind /dev/pts $LFS/dev/pts
      mount -vt proc proc $LFS/proc
      mount -vt sysfs sysfs $LFS/sys
      mount -vt tmpfs tmpfs $LFS/run
NOTE:
It is only necessary when we get to " 10. Making the LFS System Bootable" for LFS documentation
And only if you want to boot it using UEFI not legacy 

    mount -v --bind /sys/firmware/efi/efivars  $LFS/sys/firmware/efi/efivars


Maybe this mount is not always in need if it makes an error then no problem. 
And remember if you get an error in the above or below command then you must analyze it the move forward on this whole system of making your own OS.

    if [ -h $LFS/dev/shm ]; then
      mkdir -pv $LFS/$(readlink $LFS/dev/shm)
    else
      mount -t tmpfs -o nosuid,nodev tmpfs $LFS/dev/shm
    fi

Now enter into chroot environment

      chroot "$LFS" /usr/bin/env -i   \
          HOME=/root                  \
          TERM="$TERM"                \
          PS1='(lfs chroot) \u:\w\$ ' \
          PATH=/usr/bin:/usr/sbin     \
          /bin/bash --login

Note: Now your root is different from the host system. You are mounted into /mnt/lfs with usr, bin, and all the stuff inside root you see there 
are from your partially built LFS system.


 Run this after entering to chroot environment.

the first time wrote this command entering chroot, Every time no need to write it entering chroot.

    mkdir -pv /{boot,home,mnt,opt,srv}
    mkdir -pv /etc/{opt,sysconfig}
    mkdir -pv /lib/firmware
    mkdir -pv /media/{floppy,cdrom}
    mkdir -pv /usr/{,local/}{include,src}
    mkdir -pv /usr/local/{bin,lib,sbin}
    mkdir -pv /usr/{,local/}share/{color,dict,doc,info,locale,man}
    mkdir -pv /usr/{,local/}share/{misc,terminfo,zoneinfo}
    mkdir -pv /usr/{,local/}share/man/man{1..8}
    mkdir -pv /var/{cache,local,log,mail,opt,spool}
    mkdir -pv /var/lib/{color,misc,locate}


 create symbolic links...
 
    ln -sfv /run /var/run
    ln -sfv /run/lock /var/lock

    install -dv -m 0750 /root
    install -dv -m 1777 /tmp /var/tmp



NOTE:
It is only necessary when we get to " 10. Making the LFS System Bootable" for LFS documentation
And only if you want to boot it using UEFI not legacy 
if separate/boot partition then only, mine is sdc1 but you may have any

    mount /dev/sdc1 /boot/efi


After completion and exiting from chroot environment.

    logout

After exiting chroot environment unmount these 

/boot/efi only efi supported pc only if you have mounted above  

    umount -v $LFS/boot/efi
Then,

    umount -v $LFS/dev/pts
    mountpoint -q $LFS/dev/shm && umount $LFS/dev/shm
    umount -v $LFS/dev
    umount -v $LFS/run
    umount -v $LFS/proc
    umount -v $LFS/sys

only at last during setup of grub-install 


    umount -v $LFS/home
    umount -v $LFS
    
NOTE:
It is only necessary when we get to " 10. Making the LFS System Bootable" for LFS documentation
And only if you want to boot it using UEFI not legacy 

    umount -v $LFS/sys/firmware/efi/efivars
    
Now start from :
Chapter 9. System Configuration
https://www.linuxfromscratch.org/lfs/view/11.2-rc1/chapter09/introduction.html
If you want to go through legacy boot for your new OS.It is not hard if you follow the Documentation from there, if not then install EFI packages for LFS
from the BLFS page as: 10.4. Using GRUB to Set Up the Boot Process, suggests
Download all three packages available here where two of them are dependencies, you can Download optional ones also.
https://www.linuxfromscratch.org/blfs/view/svn/postlfs/grub-efi.html

locate those tar files to sources of /mnt/lfs/sources
and extract them as :
    tar -xf <filename> suppose, grub-2.06.tar.xz,
Then, make and install as shown in BLFS documentation.

And you are good to go.
