logger
====
## Intro
We enable services logdumpd_i/logdumpd_w/check_logdumpd in VZ1 devices.   
logdumpd service will write system/kernel log into /logdump partition.
- The latest log will be recorded in /logdump/logcat
- Old log will be rotated and gzipped in /logdump/logcat.001~/logdump/logcat.300

## How to turn on/off logdumpd service?
Need CMS to provide a web page with JSON format:
- {"logdumpd_level":I} : turn on logdumpd_i to write log with level Fatal/Error/Warning/Info
- {"logdumpd_level":W} : turn on logdumpd_w to write log with level Fatal/Error/Warning
- {"logdumpd_level":} : turn off logdumpd service

## How to send back log files via APP
Allow APP permissions
- allow system_app logdumpd_file:dir rw_dir_perms;   
- allow system_app logdumpd_file:file { unlink rw_file_perms };   


## log analysis
~~logdump.26a.vzh: system_WE / kernel_W / kernel_E = 0.8M / 41M / 14M~~   
~~logdump.34a.vzh: system_WE / kernel_W / kernel_E = 15M / 29M / 11M~~   
~~logdump.380.vzc: system_WE / kernel_W / kernel_E = 1.1M / 40M / 16M~~   

#### 380 SU
[2017-09-13 09:57:04] log_num: 300 (files)   
[2017-09-13 09:57:04] space_used: 38.108 (mb)   
[2017-09-13 09:57:04] record_time: 137.643 (hr) = 5.735 (days) from 09-07 16:18:24 to 09-13 09:57:00   
[2017-09-13 09:57:04] 38.108 / 137.643 = 0.277 mb/hr   
[2017-09-13 09:57:04] 300 / 137.643 = 2.180 files/hr   
[2017-09-13 09:57:04] 38.108 / 300 = 0.127 mb/file   

#### 380 signed
[2017-09-13 10:37:42] log_num: 300 (files)   
[2017-09-13 10:37:42] space_used: 36.260 (mb)   
[2017-09-13 10:37:42] record_time: 155.338 (hr) = 6.472 (days) from 09-06 23:05:18 to 09-13 10:25:35   
[2017-09-13 10:37:42] 36.260 / 155.338 = 0.233 mb/hr   
[2017-09-13 10:37:42] 300 / 155.338 = 1.931 files/hr   
[2017-09-13 10:37:42] 36.260 / 300 = 0.121 mb/file   

#### 34A signed
[2017-09-13 10:00:26] log_num: 250 (files)   
[2017-09-13 10:00:26] space_used: 29.004 (mb)   
[2017-09-13 10:00:26] record_time: 209.591 (hr) = 8.733 (days) from 09-04 16:24:51 to 09-13 10:00:18   
[2017-09-13 10:00:26] 29.004 / 209.591 = 0.138 mb/hr   
[2017-09-13 10:00:26] 250 / 209.591 = 1.193 files/hr   
[2017-09-13 10:00:26] 29.004 / 250 = 0.116 mb/file   

#### 26A signed
[2017-09-13 10:02:59] log_num: 300 (files)   
[2017-09-13 10:02:59] space_used: 40.416 (mb)   
[2017-09-13 10:02:59] record_time: 136.899 (hr) = 5.704 (days) from 09-07 17:08:57 to 09-13 10:02:52   
[2017-09-13 10:02:59] 40.416 / 136.899 = 0.295 mb/hr   
[2017-09-13 10:02:59] 300 / 136.899 = 2.191 files/hr   
[2017-09-13 10:02:59] 40.416 / 300 = 0.135 mb/file   


## todo
1. append into init.rc -> OK
2. inject into sepolicy -> X, instead, we build sepolicy
3. service oneshot ? -> NO
4. start service on which property ? -> curl web page with JSON
5. which log level is proper ?
> S F E W I D V  

## init.rc dependency
init.rc  
-> init.qcom.rc  
-> init.target.rc  
-> fstab.qcom  

## sepolicy reference
Google patches in system/sepolicy  
[7e0838aa](https://android.googlesource.com/platform/external/sepolicy/+/7e0838a%5E%21/#F0)  
[483fd267](https://android.googlesource.com/platform/external/sepolicy/+/483fd267359a457ca4ac4c4a2cbce38af6c15981%5E!/)

## build sepolicy
```
$ /data1/qct/fih_LA.UM.5.6.r1-01600-89xx.0/device/fih
$ grep line /out/target/product/VZS/obj/ETC/sepolicy_intermediates/policy.conf | grep te |less

$ cd ~/repos_qct/device/
$ scp -r build@build.kurt13.com:/data1/qct/fih_LA.UM.5.6.r1-01600-89xx.0/device/fih .
$ scp -r build@build.kurt13.com:/data1/qct/fih_LA.UM.5.6.r1-01600-89xx.0/device/fih_bsp .
$ cd ~/repos_qct/frameworks/base/services/
$ scp -r build@build.kurt13.com:/data1/qct/fih_LA.UM.5.6.r1-01600-89xx.0/frameworks/base/services/Android.mk .
$ cd ~/repos_qct/frameworks/base/services/core/
$ scp -r build@build.kurt13.com:/data1/qct/fih_LA.UM.5.6.r1-01600-89xx.0/frameworks/base/services/core/Android.mk .
$ cd ~/repos_qct/frameworks/base/services/core/java/com/
$ scp -r build@build.kurt13.com:/data1/qct/fih_LA.UM.5.6.r1-01600-89xx.0/frameworks/base/services/core/java/com/fihtdc .
$ cd ~/repos_qct/system/sepolicy/
$ scp -r build@build.kurt13.com:/data1/qct/fih_LA.UM.5.6.r1-01600-89xx.0/system/sepolicy/* .
$ cd ~/repos_qct/device/qcom/sepolicy/
$ scp -r build@build.kurt13.com:/data1/qct/fih_LA.UM.5.6.r1-01600-89xx.0/device/qcom/sepolicy/* .

$ source build/envsetup.sh ;lunch VZS_00WW_FIH-user
$ make file_contexts.bin property_contexts seapp_contexts service_contexts sepolicy -j5

```


## logcat args
```
"  -f <filename>   Log to file. Default is stdout\n"
"  -r <kbytes>     Rotate log every kbytes. Requires -f\n"
"  -n <count>      Sets max number of rotated logs to <count>, default 4\n"
"  -v <format>     Sets the log print format, where <format> is:\n"
"  -v threadtime -v usec -v printable"
"  -D              print dividers between each log buffer\n"
"  -b <buffer>     Request alternate ring buffer, 'main', 'system', 'radio',\n"
"  --buffer=<buffer> 'events', 'crash', 'default' or 'all'. Multiple -b\n"
"                  parameters are allowed and results are interleaved. The\n"
"                  default is -b main -b system -b crash.\n"
```

## Google design
#### issue
1. E klogd   : [  989.622552] init: Service logcatd does not have a SELinux domain defined.

#### logcatd.rc
system/core/logcat$ vim logcatd.rc
```
on property:logd.logpersistd.enable=true && property:logd.logpersistd=logcatd
    # all exec/services are called with umask(077), so no gain beyond 0700
    mkdir /data/misc/logd 0700 logd log
    # logd for write to /data/misc/logd, log group for read from pstore (-L)
    # b/28788401 b/30041146 b/30612424
    # exec - logd log -- /system/bin/logcat -L -b ${logd.logpersistd.buffer:-all} -v threadtime -v usec -v printable -D -f /data/misc/logd/logcat -r 1024 -n ${logd.logpersistd.size:-256}
    start logcatd

# logcatd service
service logcatd /system/bin/logcat -b ${logd.logpersistd.buffer:-all} -v threadtime -v usec -v printable -D -f /data/misc/logd/logcat -r 1024 -n ${logd.logpersistd.size:-256}
    class late_start
    disabled
    # logd for write to /data/misc/logd, log group for read from log daemon
    user logd
    group log
    writepid /dev/cpuset/system-background/tasks
```

## H design
#### issue
1. lack of selinux settings
2. no ext4 partition for record

#### init.rc
```
service recorder /sbin/recorder -s -k -P 7
    user root
    disabled
    ioprio idle 0

service recorder_rel /sbin/recorder -s -k
    user root
    disabled
    ioprio idle 0

on property:ro.build.tags=test-keys
    start recorder

on property:ro.build.tags=release-keys
    start recorder_rel
```
```
logcat -v threadtime -f /dev/recorder_default
logcat -v threadtime -f /dev/recorder_radio -b radio *:W
logcat -v threadtime -f /dev/recorder_events:q
-b event -s am_crash am_anr watchdog am_meminfo battery_level battery_status free_storage_left power_screen_state
```

## QCOM design
#### issue
1. lack of selinux settings
2. ro.logdumpd.enabled=0 for user ROM

#### partition info
/data1/tinklabs/vz1/VZH-0380-0-00WW-A03/fih/V0.380_A03/00WW/VZS-0-0380-rawprogram0.xml

program SECTOR_SIZE_IN_BYTES="512" file_sector_offset="0" filename="" **label="logdump"** num_partition_sectors="131072" physical_partition_number="0" **size_in_KB="65536.0"** sparse="false" start_byte_hex="0x164000000" start_sector="11665408"

#### default.prop
```
ro.logdumpd.enabled=1
```

#### init.qcom.rc
device/qcom/common/rootdir/etc/init.qcom.rc
```
# Logcat dump daemon, dumps logs to logdump partition
service logdumpd /system/bin/logcat -b all -v threadtime -D -w /dev/block/bootdevice/by-name/logdump
    class core 
    writepid /dev/cpuset/system-background/tasks
    seclabel u:r:logdumpd:s0
    disabled

# Logdumpd is enabled only for userdebug non-perf build
on property:ro.logdumpd.enabled=1
    start logdumpd
```
