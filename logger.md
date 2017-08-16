logger
====
## Google design

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
