# mountHardDriveToPiSamba
mountHardDriveToPiSamba

Device: Raspberry Pi 4  
OS: 2020-02-13-raspbian-buster-full.zip  
Hard Drive Format: NTFS   
[https://turbofuture.com/computers/Permanently-Mounting-a-USB-Harddrive-to-your-Raspberry-Pi] 

## Steps

1. install ntfs-3g, mount, change owner  
For newest raspbian, there is no need to install ntfs-3g and mount hard drive 
Your hard dive is mounted to /media/pi/New Volume  

If your raspbian doesn't mount for you, do it manually  
```
#Find the drive (in our case /dev/sda1)
sudo fdisk -l
#install NTFS-3g
sudo apt-get install ntfs-3g
#Make the mount directory and manage it's owner
sudo mkdir /media/pidrive
sudo chown pi:pi /media/pidrive
#Mount the drive
sudo mount -t ntfs-3g -o uid-pi,gid-pi /dev/sda1 /media/pidrive
#Now you're done but it's not permanent
 
#Making it permanent
#Edit file system table
sudo nano /etc/fstab
 
#Add this line of text after the SD card partitions
/dev/sda1   /media/pidrive  ntfs-3g uid=pi,gid=pi   0   0
#hit ctrl-o to save and ctrl-x to exit nano
 
#Now the mounting will be restored on reboot
#Reboot to test it
sudo shutdown -r now
```

2. edit smb.conf  
```
sudo nano /etc/samba/smb.conf

[toshiba]
   path = /media/pi/toshiba
   writeable=Yes
   create mask=0777
   directory mask=0777
   public=no

sudo service smbd restart
sudo ufw allow samba
```

## Notes  
How to change directory permissions in Linux  
[https://www.pluralsight.com/blog/it-ops/linux-file-permissions]  
[https://linuxize.com/post/linux-chown-command/] 
[https://askubuntu.com/questions/918379/what-is-the-main-difference-between-chmod-and-chown] 
[https://www.baeldung.com/linux/chown-chmod-permissions] 

what does chmod 2775 test mean  
SetUID, SetGID, and Sticky Bits in Linux File Permissions  
[https://linuxconfig.org/how-to-use-special-permissions-the-setuid-setgid-and-sticky-bits] 
[https://www.geeksforgeeks.org/setuid-setgid-and-sticky-bits-in-linux-file-permissions/] 
[http://linuxcommand.org/lc3_lts0090.php] 
[https://www.liquidweb.com/kb/how-do-i-set-up-setuid-setgid-and-sticky-bits-on-linux/] 

Set-user Identification (SUID)  

Have you ever thought, how a non-root user can change his own password when he does not have write permission to the /etc/shadow file. hmmm… interesting isn’t it? Well to understand the trick check for the permission of /usr/bin/passwd command :  

ls -lrt /usr/bin/passwd
-r-sr-sr-x   1 root     sys        31396 Jan 20  2014 /usr/bin/passwd

– If you check carefully, you would find the 2 S’s in the permission field. The first s stands for the SUID and the second one stands for SGID.  

– When a command or script with SUID bit set is run, its effective UID becomes that of the owner of the file, rather than of the user who is running it.  

SGID permission on executable file  

– SGID permission is similar to the SUID permission, only difference is – when the script or command with SGID on is run, it runs as if it were a member of the same group in which the file is a member.   

Sticky Bit  

– The sticky bit is primarily used on shared directories. 

– It is useful for shared directories such as /var/tmp and /tmp because users can create files, read and execute files owned by other users, but are not allowed to remove files owned by other users. 

– For example if user bob creates a file named /tmp/bob, other user tom can not delete this file even when the /tmp directory has permission of 777. If sticky bit is not set then tom can delete /tmp/bob, as the /tmp/bob file inherits the parent directory permissions. 

– root user (Off course!) and owner of the files can remove their own files. 



