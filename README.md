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

