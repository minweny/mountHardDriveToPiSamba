# mountHardDriveToPiSamba
mountHardDriveToPiSamba

Device: Raspberry Pi 4  
OS: 2020-02-13-raspbian-buster-full.zip  
Hard Drive Format: NTFS   

How to auto mount ntfs at system start 
[https://turbofuture.com/computers/Permanently-Mounting-a-USB-Harddrive-to-your-Raspberry-Pi] 

create multiple samba users 
[https://linuxize.com/post/how-to-install-and-configure-samba-on-ubuntu-18-04/] 

create one samba user: pi.  
[https://pimylifeup.com/raspberry-pi-samba/] 

## Steps

1. install ntfs-3g, mount, change owner  
For newest raspbian, there is no need to install ntfs-3g and mount hard drive 
Your hard dive is mounted to /media/pi/New Volume, which doesn't work for samba. You need to remount anyway.  
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

[pidrive]
   path = /media/pidrive
   writeable=Yes
   create mask=0777
   directory mask=0777
   public=no

sudo service smbd restart
sudo ufw allow samba

Finally, restart the Samba services with:
sudo systemctl restart smbd
sudo systemctl restart nmbd
```

## Notes  
How to change directory permissions in Linux  
[https://www.pluralsight.com/blog/it-ops/linux-file-permissions]  
[https://linuxize.com/post/linux-chown-command/] 
[https://askubuntu.com/questions/918379/what-is-the-main-difference-between-chmod-and-chown] 
[https://www.baeldung.com/linux/chown-chmod-permissions] 
[https://www.thegeekdiary.com/what-is-suid-sgid-and-sticky-bit/] 


what does chmod 2775 test mean  
SetUID, SetGID, and Sticky Bits in Linux File Permissions  
[https://linuxconfig.org/how-to-use-special-permissions-the-setuid-setgid-and-sticky-bits] 
[https://www.geeksforgeeks.org/setuid-setgid-and-sticky-bits-in-linux-file-permissions/] 
[http://linuxcommand.org/lc3_lts0090.php] 
[https://www.liquidweb.com/kb/how-do-i-set-up-setuid-setgid-and-sticky-bits-on-linux/] 
[https://blog.csdn.net/u013197629/article/details/73608613] 

Set-user Identification (SUID)  

Have you ever thought, how a non-root user can change his own password when he does not have write permission to the /etc/shadow file. hmmm… interesting isn’t it? Well to understand the trick check for the permission of /usr/bin/passwd command :  
```
ls -lrt /usr/bin/passwd
-r-sr-sr-x   1 root     sys        31396 Jan 20  2014 /usr/bin/passwd
```
– If you check carefully, you would find the 2 S’s in the permission field. The first s stands for the SUID and the second one stands for SGID.  

– When a command or script with SUID bit set is run, its effective UID becomes that of the owner of the file, rather than of the user who is running it.  

SGID permission on executable file  

– SGID permission is similar to the SUID permission, only difference is – when the script or command with SGID on is run, it runs as if it were a member of the same group in which the file is a member.   

Sticky Bit  

– The sticky bit is primarily used on shared directories. 

– It is useful for shared directories such as /var/tmp and /tmp because users can create files, read and execute files owned by other users, but are not allowed to remove files owned by other users. 

– For example if user bob creates a file named /tmp/bob, other user tom can not delete this file even when the /tmp directory has permission of 777. If sticky bit is not set then tom can delete /tmp/bob, as the /tmp/bob file inherits the parent directory permissions. 

– root user (Off course!) and owner of the files can remove their own files. 

Default, ‘somewhat secure’ permissions are commonly 755 for directories and 644 for files – no execute permissions, everyone can read, and only the user can write – you will note that the vast majority of files on a Linux system have these permissions. source: cyberx86 on Severfault

Why Files and Scripts Do Not Need Execute Permissions  

Interpreted scripts (e.g. Ruby, PHP, Python) work just fine without the execute permission. Only binaries and shell scripts need the execute bit. Stated differently, Ruby, PHP, and Python files are not directly run, but rather are read into an interpreter. Thus, only read permissions are needed to run a typical script (one that doesn’t write anything).

directory permission  
[https://codesport.io/security/the-ulitmate-guide-to-file-and-directory-permissions-on-ubuntu/] 
1. read restricts or allows viewing the directories contents, i.e. ls command
2. write restricts or allows creating new files or deleting files in the directory, i.e., rm, mv, touch
3. execute restricts or allows changing into the directory, i.e. cd command

linux show groups 
[https://websiteforstudents.com/how-to-list-all-user-groups-on-ubuntu-18-04-16-04-with-examples/] 
```
The primary user’s group is stored in the /etc/passwd file and the supplementary groups, if any, are listed in the /etc/group file..

groups
groups richard
id richard
```
