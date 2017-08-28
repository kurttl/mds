logger
====

## todo
1. append into init.rc -> OK
2. inject into sepolicy -> X, instead, we build sepolicy
3. service oneshot ? -> NO
4. start service on which property ?
5. which log level is proper ?
> S F E W I D V  
> W: 17 hours  

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
