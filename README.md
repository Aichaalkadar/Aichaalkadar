on post-fs-data
    mkdir /data/dumpsys 0775 system system
    mkdir /data/core 0775 system system
    mkdir /data/misc/aee_interim 0775 system system

on post-fs
    start aee_aed
    start aee_aed64

on property:ro.build.type=eng
    write /proc/sys/fs/suid_dumpable 1
    write /proc/sys/kernel/core_pattern "|/system/bin/aee_core_forwarder /data/core/ %p %s UID=%u GID=%g"
    write /proc/sys/kernel/core_pipe_limit 3

on property:ro.build.type=userdebug
    write /proc/sys/fs/suid_dumpable 1
    write /proc/sys/kernel/core_pattern "|/system/bin/aee_core_forwarder /data/core/ %p %s UID=%u GID=%g"
    write /proc/sys/kernel/core_pipe_limit 3

on property:persist.vendor.aeev.core.dump=enable
    write /proc/sys/fs/suid_dumpable 1
    write /proc/sys/kernel/core_pattern "|/system/bin/aee_core_forwarder /data/core/ %p %s UID=%u GID=%g"
    write /proc/sys/kernel/core_pipe_limit 3

on property:ro.build.type=eng && property:persist.vendor.aee.tracing_on=0
    write /sys/kernel/debug/tracing/tracing_on 0

on property:ro.build.type=eng && property:persist.vendor.aee.tracing_on=1
    write /sys/kernel/debug/tracing/tracing_on 1

on property:ro.build.type=userdebug && property:persist.vendor.aee.tracing_on=0
    write /sys/kernel/debug/tracing/tracing_on 0

on property:ro.build.type=userdebug && property:persist.vendor.aee.tracing_on=1
    write /sys/kernel/debug/tracing/tracing_on 1

on init
    write /proc/self/coredump_filter 39
   
on property:ro.build.version.type=""
    export LD_PRELOAD libdirect-coredump.so

on property:ro.build.version.type=longcheer
    export LD_PRELOAD libdirect-coredump.so

on property:sys.boot_completed=1
    start aee-reinit

import /init.preload.rc
