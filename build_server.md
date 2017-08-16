build server
====

## server
```
$ ssh -lbuild build.kurt13.com
$ ssh -lgeorge build.kurt13.com
```

## add user
```
$ sudo adduser george
$ sudo usermod -g new_group user_name
```
## path
aosp: /data1/aosp/android-7.0.0_r1/  
qct: /data1/qct/LA.UM.5.6.r1-01600-89xx.0/  
frameworks: /data1/tinklabs/vz1/VZH-0380-0-00WW-A03/  

## repo sync
#### For AOSP
[Android Developers](https://source.android.com/source/downloading)
```
$ repo init -u https://android.googlesource.com/platform/manifest -b android-7.1.2_r33
$ repo sync
```

#### For MSM8917
[CodeAurora](https://wiki.codeaurora.org/xwiki/bin/QAEP/release)
```
$ repo init -u git://codeaurora.org/platform/manifest.git -b release -m LA.UM.5.6.r1-01600-89xx.0.xml
$ repo sync
```
## storage usage
Do not sync codebase into storage with type non-ext4 / exfat / NTFS

| type     | occupy |
| :------- | ----: |
| Google .repo     | 38 GB |
| Google checkout  | 22 GB |
| Google out folder| 45 GB |
| msm8917 .repo    | 48 GB |
| msm8917 checkout | 21 GB |

## build command
#### For AOSP
[Build AOSP Nougat 7.0](https://developer.sonymobile.com/open-devices/aosp-build-instructions/how-to-build-aosp-nougat-for-unlocked-xperia-devices/build-aosp-nougat-7-0/)
dell-xps-13-9360 spends **3.5 hours** to full build aosp android-7.1.2
```
$ source build/envsetup.sh && lunch aosp_arm64-eng
$ make -j4 2>&1 |tee out-log.txt
```
#### For MSM8917
```
$ source build/envsetup.sh && lunch msm8937_64-userdebug
$ make -j4 2>&1 |tee out-log.txt
```

## boot image
#### break
```
$ cd bootimgtool
$ cp boot_xxx.img ./boot.img
$ ./break_boot_image.sh
(Check ./out/ramdisk)
```
#### combine
```
$ cd bootimgtool
(Modify ./out/ramdisk)
$ ./combine_boot_image.sh
```
#### sign
```
$ ./boot_signer /boot $PATH_TO_BOOT_IMAGE ./sign/TinkLabs.pkcs8 ./sign/TinkLabs.x509.pem $PATH_TO_BOOT_IMAGE_SIGNED
```
