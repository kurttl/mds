flash steps
======

## Environment Setup
 - Download the latest [fastboot](https://developer.android.com/studio/releases/platform-tools.html)
 - Download adbkeys [here](https://drive.google.com/a/tinklabs.com/file/d/0B4v-joXi24b4MFBlSDdHaHJIcHlTNm9XQlpjMWZRQzBDbkNV/view?usp=sharing)  
  -- Unzip and move to ~/.android/
 - Modify 51-android.rules  
  -- https://gist.github.com/giacobenin/1255038  
  -- $ sudo vim /etc/udev/rules.d/51-android.rules  
  -- $ sudo udevadm control --reload-rules  

## Flash Steps
```
$ fastboot oem fih on
$ fastboot oem devlock key
$ fastboot -w
$ fastboot flash boot boot.img
$ fastboot flash system system.img
$ fastboot reboot
(waiting for optimization)
Press left corner bottom to setup wifi
```

## Remount system
Download the [su file](https://drive.google.com/file/d/0B-Yu3eNIy3kgZi1FTWh1YTRvTGM/view)
```
$ adb push su /data/local/tmp/su
$ adb shell
$ chmod 755 /data/local/tmp/su
$ /data/local/tmp/su
# mount -o rw,remount /system
```
