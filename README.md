# Build-your-own-os
Linux from scratch (Linux 5.19.2-lfs-11.2-rc1)


To create your custom Linux system, you are encouraged to follow the step-by-step guide available at Linux From Scratch (LFS) 11.2-rc1. If you prefer, you can begin the process from the middle stages to avoid lengthy compilation times. For this purpose, pre-compiled patches are available for the GCC 11+ and Linux 5.19.2-lfs-11.2-rc1 kernel.


 
The below-named tar file contains all the necessary packages with the latest GCC 11+, Linux 5.19.2-lfs-11.2-rc1

upto_before_8.82_stripping_-11.2-rc1.tar.xz 


# Download link .

https://drive.google.com/file/d/1Z7G1QxDR_kjUPTcWG5YfwythVx0-hRyv/view?usp=sharing





After extracting it as 

    tar -xpf $HOME/lfs-temp-tools-11.2-rc1.tar.xz

For a safer approach, it's recommended to carefully read the LFS documentation before proceeding, as an incorrect choice in disk selection might result in data loss.

It's advisable to use a 4GB Pendrive for convenience. Follow these steps:
          
 Warning  ...
For a safer approach, it's recommended to carefully read the LFS documentation before proceeding, as an incorrect choice in disk selection might result in data loss.

It's advisable to use a 4GB Pendrive for convenience. Follow these steps:
Open terminal in linux system 
Accessing sudo power

      sudo -i

First, run as root.Then only copy below ...

Identify your disk using | Don't Hurry if you didn't get commands , man page is always there to help . Eg: man lsblk 

      lsblk
Once identified, mount the disk where the LFS tar was extracted, The Below is basic script to identify the right disk.


       echo " \033[34m Please identify your disk as per their space and nb of partitions used ..\n Use the disk or partition where LFS tar was        extracted."
       echo" \n\n Give your disk identification /dev/sd?? to be mounted and which contains LFS Project \n \033[37m Eg: sdb3 or sdc1 or sdd4 \033[0m "
       read a
       mkdir -pv /mnt/lfs
       mount /dev/$a /mnt/lfs
       print $a
       export LFS=/mnt/lfs
       
To enter the chroot environment and execute commands:

      chown -R root:root $LFS/{usr,lib,var,etc,bin,sbin}
      case $(uname -m) in
        x86_64) chown -R root:root $LFS/lib64 ;;
      esac
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


Please note that the steps described below are necessary only when you reach the stage "10. Making the LFS System Bootable" in the LFS documentation. These steps are specifically for setting up the boot process for UEFI-based systems, not for legacy systems. Additionally, these actions are applicable if you have a separate boot partition; the example given is 'sdc1', but your system might have a different configuration.

If you are not planning to boot your Linux system using UEFI or if you do not have a separate boot partition, these steps may not be required for your setup.

For instance, to configure for UEFI boot and if you have a separate boot partition, such as 'sdc1':

    mount /dev/sdc1 /boot/efi
    
Please ensure to adapt these steps according to your system's requirements and the chosen boot configuration.

These steps are crucial for setting up the boot process only if you're intending to boot your LFS system using UEFI and have a separate boot partition. Adjust the commands and mount points as per your system's specifics.


After completion and exiting from chroot environment.

    logout
After exiting chroot environment Unmount the partitions 

    umount -v $LFS/boot/efi       # Optional case as describe above
Then,

    umount -v $LFS/dev/pts
    mountpoint -q $LFS/dev/shm && umount $LFS/dev/shm
    umount -v $LFS/dev
    umount -v $LFS/run
    umount -v $LFS/proc
    umount -v $LFS/sys
    umount -v $LFS/home
    umount -v $LFS
    
NOTE:
It is only necessary when we get to " 10. Making the LFS System Bootable" for LFS documentation
And only if you want to boot it using UEFI not legacy 

    umount -v $LFS/sys/firmware/efi/efivars
    
If you covered above part , then 
You can start configuring your system from Chapter 9: System Configuration onward. If you prefer legacy boot, follow the documentation from there. For UEFI boot, install the EFI packages as described in BLFS section 10.4 Using GRUB to Set Up the Boot Process.
https://www.linuxfromscratch.org/blfs/view/svn/postlfs/grub-efi.html

Download the necessary packages and extract them into /mnt/lfs/sources:
tar -xf grub-2.06.tar.xz  # Example package name

Follow the BLFS documentation to compile and install these packages.

Now, you're ready to go!


