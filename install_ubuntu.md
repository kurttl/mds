ubuntu 16.04
===========

##Issues
[Install Ubuntu on Dell-xps-13-9360](https://askubuntu.com/questions/867488/dell-xps-13-9360-dualboot-windows-10-and-ubuntu-16-04)
Dell XPS 13 2016 (intel i5 9360, KabyLake) **has SATA operation in RAID mode**, which doesnt' work for Ubuntu. You need to change it to AHCI. Also disable secureboot so you can see usb and install it. You may need to add usb in boot sequence as option. In windows you need to disable fastboot so Ubuntu can recognize partitions correctly.


----------


##Steps
 - Prepare unused partition for Ubuntu:
 [Install Ubuntu 3](https://read01.com/zh-tw/jND7m.html#.WYxLtTAjHaW)
  1. Only keep 100 GB in C:\ for Windows 10 NTFS partition.
  2. Separate rest 400 GB from C:\ for Ubuntu EXT4
 - Setting up live-ubuntu USB:
 [live-ubuntu USB](http://blog.xuite.net/yh96301/blog/57645340-Ubuntu+16.04%E8%A3%BD%E4%BD%9CLive+USB%E9%9A%A8%E8%BA%AB%E7%A2%9F%E7%9A%84%E8%BB%9F%E9%AB%94Unetbootin)
  1. Download UNetbootin and Ubuntu.
  2. Use UNetbootin to add ubuntu in the usb(min 6-8GB?)(another usb than the one with recovery): choose Uefi option.
 - Installing Windows in AHCI mode:
 (This step will cause fail to boot to Windows OS)
  1. restart, open bios(F2) when DELL logo comes up.
  2. Change secure boot disbled: BIOS->Settings->Secure Boot->Disabled.
  3. Change Sata Operation to AHCI: BIOS->Settinsg->System
  4. **Configuration->SATA Operation->AHCI.**
  5. Click "Apply"-button at bottom right in bios.
  6. If Windows loads you're golden, if not select: more options, and somewhere in there is "restore with default settings" or **"reinstall Windows 10".**
 - Actions in Windows 10:
  1. You may try to install Windows with your recovery USB too. (you may need to add the usb to boot sequence in BIOS)
  2. Now if your Windows starts up you need to do one more thing so Ubuntu can recognize partitions correctly.
  3. Disable fastboot in Windows: Power Options->Choose what the power buttons do->Change settings that are currently unavailable->uncheck "Turn on fast startup"
 - Installing Ubuntu:
  1. You may need to add USB to boot-sequence if it doesn't load when you restart with ubuntu-USB on laptop: go to BIOS(F2 on dell logo)->Settings->Boot Sequence->"Add Boot Option"-button, add name and choose option with usb in File System List, not the one with "Pci"-in the name.. click "OK".
  2. Select in Boot Sequence your usb and move it all the way up with the arrow buttons, so it boots first.
  3. Then in BIOS click "Apply" and "Exit". This will restart your laptop.
Now you should boot up in grub like environment, select "try ubuntu".
  4. Open Gparted, resize biggest partition so we can fit Ubuntu.
	  Some web teach you to define 4 partitions:
	  [Install Ubuntu 2](https://sammycomp.wordpress.com/2015/09/03/linux%E5%B9%B3%E5%8F%B0%E4%BD%BF%E7%94%A8ssd%E5%BF%85%E8%AE%80-%E7%AF%84%E4%BE%8B%E7%82%BAubuntu/)
	   [Install Ubuntu 3](https://read01.com/zh-tw/jND7m.html#.WYxLtTAjHaW)
	  > / 20GB 主要磁區
	  > /home 380GB 延伸磁區
	  > /boot 200MB 延伸磁區
	  > SWAP 256MB 延伸磁區
	  
	  **But suggest using simple setting with 2 partitions:**
	  [Install Ubuntu 1](http://wiki.ubuntu-tw.org/index.php?title=UbuntuInstallNEW)
	  > / 400GB 主要磁區
	  > SWAP 256MB 延伸磁區
  5. Run the Ubuntu Installer. Do your configurations, select all update options. Choose the option with "along side Windows".
  6. Now you should have Windows and Ubuntu on your laptop!


----------


