flash steps
======

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
