logger for handy
====

## QCOM design
#### partition info
/data1/tinklabs/vz1/VZH-0380-0-00WW-A03/fih/V0.380_A03/00WW/VZS-0-0380-rawprogram0.xml

<!--program SECTOR_SIZE_IN_BYTES="512" file_sector_offset="0" filename="" **label="logdump"** num_partition_sectors="131072" physical_partition_number="0" **size_in_KB="65536.0"** sparse="false" start_byte_hex="0x164000000" start_sector="11665408"/>

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
