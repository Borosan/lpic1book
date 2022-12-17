# 101.1. Determine and configure hardware settings

## **101.1 Determine and configure hardware settings**

**Weight:**2

**Description: **Candidates should be able to determine and configure fundamental system hardware.

**Key Knowledge Areas:**

* Tools and utilities to list various hardware information (e.g. lsusb, lspci, etc.)
* Tools and utilities to manipulate USB devices
* Conceptual understanding of sysfs, udev, dbus

**The following is a partial list of the used files, terms and utilities:**

* /sys/
* /proc/
* /dev/
* modprobe
* lsmod
* lspci
* lsusb

Linux treat every thing as a file. It includes programs , hardware and even processes which are running. These files are organized in directories and standardize for easier access and administration. Lets see how linux dealing with devices:

### /proc

The /proc is a virtual dicretory which contains a illusionary filesystem called procfs. It does not exist on a disk. Instead, the kernel creates it in memory. It is used to provide information about the system (originally about processes).

```
root@ubuntu16-1:~# ls /proc/
1     17    186   20    215   232   26    4043  847        diskstats    pagetypeinfo
10    1705  187   200   2152  233   2600  452   85         dma          partitions
1002  1717  188   201   2154  234   2625  4773  86         driver       sched_debug
1023  1757  189   2011  2159  235   2678  5054  87         execdomains  schedstat
1057  1761  1890  202   216   236   27    5481  873        fb           scsi
1074  1763  1897  2026  217   237   271   5913  874        filesystems  self
1075  1765  19    2027  218   238   272   5999  876        fs           slabinfo
1077  1769  190   2029  219   2383  28    6     879        interrupts   softirqs
1078  1773  1906  203   22    239   289   6000  88         iomem        stat
1092  1780  191   2030  220   24    296   6124  881        ioports      swaps
1095  1788  1912  2032  2200  240   32    6313  882        irq          sys
11    1789  1913  2037  2205  241   3272  6317  885        kallsyms     sysrq-trigger
117   1796  1915  204   221   242   33    6350  887        kcore        sysvipc
1176  1798  1916  205   2215  243   334   6355  89         keys         thread-self
12    18    192   206   222   2456  335   7     9          key-users    timer_list
1221  180   1929  207   223   2474  34    729   934        kmsg         timer_stats
13    1806  193   208   224   2477  3462  76    95         kpagecgroup  tty
1381  181   1934  209   2241  2479  3473  77    983        kpagecount   uptime
14    1812  194   2092  225   2490  3474  78    986        kpageflags   version
1480  182   195   2095  2257  2496  35    79    acpi       loadavg      version_signature
15    1823  1952  21    226   25    36    8     asound     locks        vmallocinfo
1532  1826  1955  210   227   2506  370   80    buddyinfo  mdstat       vmstat
1553  183   1957  211   228   2507  3892  81    bus        meminfo      zoneinfo
1574  1836  196   212   229   2521  3907  82    cgroups    misc
1575  1838  1962  2122  23    2525  3908  83    cmdline    modules
1589  184   197   2124  230   2533  3909  830   consoles   mounts
1593  1845  198   213   2301  2534  392   84    cpuinfo    mpt
16    185   199   2138  231   2589  3981  840   crypto     mtrr
1698  1851  2     214   2311  2599  4     843   devices    net
```

Some of the more important files and directories are :

**/proc/1:** A directory with information about process number 1. Each process has a directory below /proc with the name being its process identification number.

**/proc/cpuinfo: **Information about the processor, such as its type, make, model, and performance.

```
root@ubuntu16-1:~# cat /proc/cpuinfo 
processor    : 0
vendor_id    : GenuineIntel
cpu family    : 6
model        : 142
model name    : Intel(R) Core(TM) i7-7500U CPU @ 2.70GHz
stepping    : 9
microcode    : 0x8e
cpu MHz        : 2904.002
cache size    : 4096 KB
physical id    : 0
siblings    : 1
core id        : 0
cpu cores    : 1
apicid        : 0
initial apicid    : 0
fpu        : yes
fpu_exception    : yes
cpuid level    : 22
wp        : yes
flags        : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ss syscall nx pdpe1gb rdtscp lm constant_tsc arch_perfmon nopl xtopology tsc_reliable nonstop_tsc pni pclmulqdq ssse3 fma cx16 pcid sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand hypervisor lahf_lm abm 3dnowprefetch fsgsbase tsc_adjust bmi1 avx2 smep bmi2 invpcid mpx rdseed adx smap clflushopt xsaveopt xsavec xsaves arat
bugs        :
bogomips    : 5808.00
clflush size    : 64
cache_alignment    : 64
address sizes    : 43 bits physical, 48 bits virtual
power management:
```

**/proc/filesystems : **Filesystems configured into the kernel.

```
root@blackfox:~# cat /proc/filesystems 
nodev	sysfs
nodev	rootfs
nodev	ramfs
nodev	bdev
nodev	proc
nodev	cpuset
nodev	cgroup
nodev	cgroup2
nodev	tmpfs
nodev	devtmpfs
nodev	configfs
nodev	debugfs
nodev	tracefs
nodev	securityfs
nodev	sockfs
nodev	dax
nodev	bpf
nodev	pipefs
nodev	hugetlbfs
nodev	devpts
	ext3
	ext2
	ext4
	squashfs
	vfat
nodev	ecryptfs
	fuseblk
nodev	fuse
nodev	fusectl
nodev	pstore
nodev	efivarfs
nodev	mqueue
nodev	autofs
nodev	binfmt_misc

```

**/proc/interrupts: **Shows which interrupts are in use, and how many of each there have been.

```
root@ubuntu16-1:~# cat /proc/interrupts 
            CPU0       
   0:          7   IO-APIC    2-edge      timer
   1:       7113   IO-APIC    1-edge      i8042
   8:          1   IO-APIC    8-edge      rtc0
   9:          0   IO-APIC    9-fasteoi   acpi
  12:      24004   IO-APIC   12-edge      i8042
  14:          0   IO-APIC   14-edge      ata_piix
  15:          0   IO-APIC   15-edge      ata_piix
  16:       1156   IO-APIC   16-fasteoi   vmwgfx, snd_ens1371
  17:      95298   IO-APIC   17-fasteoi   ehci_hcd:usb1, ioc0
  18:         68   IO-APIC   18-fasteoi   uhci_hcd:usb2
  19:      34519   IO-APIC   19-fasteoi   ens33
  24:          0   PCI-MSI 344064-edge      PCIe PME, pciehp
  25:          0   PCI-MSI 346112-edge      PCIe PME, pciehp
  26:          0   PCI-MSI 348160-edge      PCIe PME, pciehp
  27:          0   PCI-MSI 350208-edge      PCIe PME, pciehp
  28:          0   PCI-MSI 352256-edge      PCIe PME, pciehp
  29:          0   PCI-MSI 354304-edge      PCIe PME, pciehp
  30:          0   PCI-MSI 356352-edge      PCIe PME, pciehp
  31:          0   PCI-MSI 358400-edge      PCIe PME, pciehp
  32:          0   PCI-MSI 360448-edge      PCIe PME, pciehp
  33:          0   PCI-MSI 362496-edge      PCIe PME, pciehp
  34:          0   PCI-MSI 364544-edge      PCIe PME, pciehp
  35:          0   PCI-MSI 366592-edge      PCIe PME, pciehp
  36:          0   PCI-MSI 368640-edge      PCIe PME, pciehp
  37:          0   PCI-MSI 370688-edge      PCIe PME, pciehp
  38:          0   PCI-MSI 372736-edge      PCIe PME, pciehp
  39:          0   PCI-MSI 374784-edge      PCIe PME, pciehp
  40:          0   PCI-MSI 376832-edge      PCIe PME, pciehp
  41:          0   PCI-MSI 378880-edge      PCIe PME, pciehp
  42:          0   PCI-MSI 380928-edge      PCIe PME, pciehp
  43:          0   PCI-MSI 382976-edge      PCIe PME, pciehp
  44:          0   PCI-MSI 385024-edge      PCIe PME, pciehp
  45:          0   PCI-MSI 387072-edge      PCIe PME, pciehp
  46:          0   PCI-MSI 389120-edge      PCIe PME, pciehp
  47:          0   PCI-MSI 391168-edge      PCIe PME, pciehp
  48:          0   PCI-MSI 393216-edge      PCIe PME, pciehp
  49:          0   PCI-MSI 395264-edge      PCIe PME, pciehp
  50:          0   PCI-MSI 397312-edge      PCIe PME, pciehp
  51:          0   PCI-MSI 399360-edge      PCIe PME, pciehp
  52:          0   PCI-MSI 401408-edge      PCIe PME, pciehp
  53:          0   PCI-MSI 403456-edge      PCIe PME, pciehp
  54:          0   PCI-MSI 405504-edge      PCIe PME, pciehp
  55:          0   PCI-MSI 407552-edge      PCIe PME, pciehp
  56:      10472   PCI-MSI 1130496-edge      ahci[0000:02:05.0]
  57:       2737   PCI-MSI 129024-edge      vmw_vmci
  58:          0   PCI-MSI 129025-edge      vmw_vmci
 NMI:          0   Non-maskable interrupts
 LOC:    1023154   Local timer interrupts
 SPU:          0   Spurious interrupts
 PMI:          0   Performance monitoring interrupts
 IWI:          0   IRQ work interrupts
 RTR:          0   APIC ICR read retries
 RES:          0   Rescheduling interrupts
 CAL:          0   Function call interrupts
 TLB:          0   TLB shootdowns
 TRM:          0   Thermal event interrupts
 THR:          0   Threshold APIC interrupts
 DFR:          0   Deferred Error APIC interrupts
 MCE:          0   Machine check exceptions
 MCP:         69   Machine check polls
 ERR:          0
 MIS:          0
 PIN:          0   Posted-interrupt notification event
 PIW:          0   Posted-interrupt wakeup event
```

**/proc/meminfo : **Information about memory usage, both physical and swap.

```
root@ubuntu16-1:~# cat /proc/meminfo 
MemTotal:         994868 kB
MemFree:          114032 kB
MemAvailable:     189404 kB
Buffers:           12060 kB
Cached:           180636 kB
SwapCached:        22484 kB
Active:           264988 kB
Inactive:         271148 kB
Active(anon):     167716 kB
Inactive(anon):   179188 kB
Active(file):      97272 kB
Inactive(file):    91960 kB
Unevictable:          32 kB
Mlocked:              32 kB
SwapTotal:       1045500 kB
SwapFree:         701288 kB
Dirty:                 0 kB
Writeback:             0 kB
AnonPages:        325912 kB
Mapped:           124724 kB
Shmem:              3464 kB
Slab:              73812 kB
SReclaimable:      27344 kB
SUnreclaim:        46468 kB
KernelStack:        9436 kB
PageTables:        32112 kB
NFS_Unstable:          0 kB
Bounce:                0 kB
WritebackTmp:          0 kB
CommitLimit:     1542932 kB
Committed_AS:    3646596 kB
VmallocTotal:   34359738367 kB
VmallocUsed:           0 kB
VmallocChunk:          0 kB
HardwareCorrupted:     0 kB
AnonHugePages:    157696 kB
ShmemHugePages:        0 kB
ShmemPmdMapped:        0 kB
CmaTotal:              0 kB
CmaFree:               0 kB
HugePages_Total:       0
HugePages_Free:        0
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:       2048 kB
DirectMap4k:      120704 kB
DirectMap2M:      927744 kB
DirectMap1G:           0 kB
```

The way /proc virtual directory organize processes get noticed by developers and they started to use /proc for both reading and writing information. So guess what, little by little /proc become a place which stored different kind of information. Processes information, current running Kernel information, hardware information, system information. Some one should do some thing to stop this messy place and that cause /sys introducing in kernel 2.5 .

### /sys

/sys it is a virtual directory with illusionary sysfs file system, which is created when system boots up and get vanished when system restarts or goes off.

sysfs introduced to specifically store system information and its components (mostly attached and installed hardware). An as it was planned for that its seems more organized and more standardize than procfs.

```
root@ubuntu16-1:~# ls -l /sys/
total 0
drwxr-xr-x   2 root root 0 Oct 24 06:07 block
drwxr-xr-x  38 root root 0 Oct 24 06:07 bus
drwxr-xr-x  60 root root 0 Oct 24 06:07 class
drwxr-xr-x   4 root root 0 Oct 24 06:07 dev
drwxr-xr-x  14 root root 0 Oct  9 05:54 devices
drwxr-xr-x   5 root root 0 Oct 24 06:07 firmware
drwxr-xr-x   8 root root 0 Oct  9 05:54 fs
drwxr-xr-x   2 root root 0 Oct 24 06:07 hypervisor
drwxr-xr-x  11 root root 0 Oct  9 05:54 kernel
drwxr-xr-x 146 root root 0 Oct 24 06:07 module
drwxr-xr-x   2 root root 0 Oct 24 06:07 power

root@ubuntu16-1:~# ls /sys/devices/
breakpoint  isa          msr         platform  software  tracepoint
cpu         LNXSYSTM:00  pci0000:00  pnp0      system    virtual
```

sysfs hasn't caused all the stuff move from /proc to /sys , they still exist in /proc but /sys gives us a better view of current data.

{% hint style="info" %}
**Linux kernel modules** (LKMs) are pieces of code which can be loaded into the kernel much like a hot-swappable piece of hardware. they can be inserted into the kernel and activated without the system needing to be rebooted.
{% endhint %}

### udev

The kernel is the central part of operating system to address the hardware. And to make sure that the hardware is available for the kernel udev plays an important role.

udev is a replacement for the Device File System (DevFS) starting with the Linux 2.6 kernel series. udev plays role in Loading Kernel Module, Creating Device Files and making sure every thing is the order we need it to be. Lets see how it works:

![](.gitbook/assets/hw-udev.jpg)

1. The linux kernel initiates the device loading and next sends out messages (uevents) to the udev daemon.
2. udev daemon catches the event and decide how to handle based on the attributes that it has received in the event. udev load required kernel module with necessary information using **modprobe**.

{% hint style="success" %}
what is modprobe?

**modprobe** is an intelligent command for listing, inserting as well as removing modules from the kernel.( Will be explained )
{% endhint %}

3 . udev next reads its rules . udev allows us to ban devices based on their properties, like vendor ID and device ID, ... .

* Default rules are in /lib/udev/rules.d
* Custom rules are in /etc/udev/rules.d

Lets see it in action, we use and then attach a usb storage:

```
root@ubuntu16-1:~# udevadm monitor 
monitor will print the received events for:
UDEV - the event which udev sends out after rule processing
KERNEL - the kernel uevent

KERNEL[12768.963188] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1 (usb)
KERNEL[12768.972503] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0 (usb)
UDEV  [12769.092526] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1 (usb)
KERNEL[12769.206478] add      /module/usb_storage (module)
UDEV  [12769.218392] add      /module/usb_storage (module)
KERNEL[12769.220850] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0/host33 (scsi)
KERNEL[12769.221595] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0/host33/scsi_host/host33 (scsi_host)
KERNEL[12769.223741] add      /bus/usb/drivers/usb-storage (drivers)
KERNEL[12769.225915] add      /module/uas (module)
KERNEL[12769.227659] add      /bus/usb/drivers/uas (drivers)
UDEV  [12769.227861] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0 (usb)
UDEV  [12769.244207] add      /bus/usb/drivers/usb-storage (drivers)
UDEV  [12769.246246] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0/host33 (scsi)
UDEV  [12769.247844] add      /module/uas (module)
UDEV  [12769.253961] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0/host33/scsi_host/host33 (scsi_host)
UDEV  [12769.256450] add      /bus/usb/drivers/uas (drivers)
KERNEL[12770.248662] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0/host33/target33:0:0 (scsi)
UDEV  [12770.250053] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0/host33/target33:0:0 (scsi)
KERNEL[12770.251421] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0/host33/target33:0:0/33:0:0:0 (scsi)
KERNEL[12770.251639] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0/host33/target33:0:0/33:0:0:0/scsi_disk/33:0:0:0 (scsi_disk)
KERNEL[12770.252615] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0/host33/target33:0:0/33:0:0:0/scsi_device/33:0:0:0 (scsi_device)
UDEV  [12770.253448] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0/host33/target33:0:0/33:0:0:0 (scsi)
KERNEL[12770.253478] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0/host33/target33:0:0/33:0:0:0/scsi_generic/sg2 (scsi_generic)
KERNEL[12770.253663] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0/host33/target33:0:0/33:0:0:0/bsg/33:0:0:0 (bsg)
UDEV  [12770.256761] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0/host33/target33:0:0/33:0:0:0/scsi_disk/33:0:0:0 (scsi_disk)
UDEV  [12770.262849] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0/host33/target33:0:0/33:0:0:0/scsi_device/33:0:0:0 (scsi_device)
UDEV  [12770.263142] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0/host33/target33:0:0/33:0:0:0/bsg/33:0:0:0 (bsg)
UDEV  [12770.263288] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0/host33/target33:0:0/33:0:0:0/scsi_generic/sg2 (scsi_generic)
KERNEL[12770.264594] add      /devices/virtual/bdi/8:16 (bdi)
UDEV  [12770.265053] add      /devices/virtual/bdi/8:16 (bdi)
KERNEL[12770.287541] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0/host33/target33:0:0/33:0:0:0/block/sdb (block)
KERNEL[12770.288023] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0/host33/target33:0:0/33:0:0:0/block/sdb/sdb1 (block)
UDEV  [12770.642686] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0/host33/target33:0:0/33:0:0:0/block/sdb (block)
UDEV  [12770.829877] add      /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0/host33/target33:0:0/33:0:0:0/block/sdb/sdb1 (block)
KERNEL[12771.061812] add      /module/nls_iso8859_1 (module)
UDEV  [12771.063716] add      /module/nls_iso8859_1 (module)
```

udev write device information to the /sys virtual directory. Also udev works as an Hardware Abstaraction Layer(HAL) and creates device file entries under /dev directory in a structured way.

{% hint style="success" %}
What is HAL? In computers, a **hardware abstraction layer** (**HAL**) is a **layer** of programming that allows a computer OS to interact with a **hardware** device at a general or **abstract level** rather than at a detailed **hardware level**.
{% endhint %}

another example:

```
root@ubuntu16-1:~# udevadm info --query=all --name=/dev/sdb
P: /devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0/host33/target33:0:0/33:0:0:0/block/sdb
N: sdb
S: disk/by-id/usb-Kingston_DT_101_II_0013729982D5B97196320049-0:0
S: disk/by-path/pci-0000:02:03.0-usb-0:1:1.0-scsi-0:0:0:0
E: DEVLINKS=/dev/disk/by-path/pci-0000:02:03.0-usb-0:1:1.0-scsi-0:0:0:0 /dev/disk/by-id/usb-Kingston_DT_101_II_0013729982D5B97196320049-0:0
E: DEVNAME=/dev/sdb
E: DEVPATH=/devices/pci0000:00/0000:00:11.0/0000:02:03.0/usb1/1-1/1-1:1.0/host33/target33:0:0/33:0:0:0/block/sdb
E: DEVTYPE=disk
E: ID_BUS=usb
E: ID_INSTANCE=0:0
E: ID_MODEL=DT_101_II
E: ID_MODEL_ENC=DT\x20101\x20II\x20\x20\x20\x20\x20\x20\x20
E: ID_MODEL_ID=1625
E: ID_PART_TABLE_TYPE=dos
E: ID_PART_TABLE_UUID=de86c489
E: ID_PATH=pci-0000:02:03.0-usb-0:1:1.0-scsi-0:0:0:0
E: ID_PATH_TAG=pci-0000_02_03_0-usb-0_1_1_0-scsi-0_0_0_0
E: ID_REVISION=PMAP
E: ID_SERIAL=Kingston_DT_101_II_0013729982D5B97196320049-0:0
E: ID_SERIAL_SHORT=0013729982D5B97196320049
E: ID_TYPE=disk
E: ID_USB_DRIVER=usb-storage
E: ID_USB_INTERFACES=:080650:
E: ID_USB_INTERFACE_NUM=00
E: ID_VENDOR=Kingston
E: ID_VENDOR_ENC=Kingston
E: ID_VENDOR_ID=0951
E: MAJOR=8
E: MINOR=16
E: SUBSYSTEM=block
E: TAGS=:systemd:
E: USEC_INITIALIZED=12770500291
```

We have seen this information previously. Try `udevadm info --attribute-walk --name=/dev/sda` for your self. These device attributes can be used in udev rules.

### /dev

This directory contains the **device files** for every hardware device attached to the system.

> **Device files** are employed to provide the operating system and users an interface to the devices that they represent.

/dev exits from early beginning versions of linux and it was populated by devfs. (As we mentioned) devfs was a an **obsolete and no longer available.**

 These days, it has been replaced by udev, a daemon that manages the contents of /dev in a temporary filesystem, **(**or by devtmpfs, which is a lightweight replacement for devfs that is used in some minimal systems).

```
root@ubuntu16-1:~# ls /dev/
agpgart          hwrng               port      tty10  tty33  tty56      ttyS2    vcs1
autofs           initctl             ppp       tty11  tty34  tty57      ttyS20   vcs2
block            input               psaux     tty12  tty35  tty58      ttyS21   vcs3
bsg              kmsg                ptmx      tty13  tty36  tty59      ttyS22   vcs4
btrfs-control    lightnvm            pts       tty14  tty37  tty6       ttyS23   vcs5
bus              log                 random    tty15  tty38  tty60      ttyS24   vcs6
cdrom            loop0               rfkill    tty16  tty39  tty61      ttyS25   vcs7
cdrw             loop1               rtc       tty17  tty4   tty62      ttyS26   vcsa
char             loop2               rtc0      tty18  tty40  tty63      ttyS27   vcsa1
console          loop3               sda       tty19  tty41  tty7       ttyS28   vcsa2
core             loop4               sda1      tty2   tty42  tty8       ttyS29   vcsa3
cpu_dma_latency  loop5               sda2      tty20  tty43  tty9       ttyS3    vcsa4
cuse             loop6               sda5      tty21  tty44  ttyprintk  ttyS30   vcsa5
disk             loop7               sg0       tty22  tty45  ttyS0      ttyS31   vcsa6
dmmidi           loop-control        sg1       tty23  tty46  ttyS1      ttyS4    vcsa7
dri              mapper              shm       tty24  tty47  ttyS10     ttyS5    vfio
dvd              mcelog              snapshot  tty25  tty48  ttyS11     ttyS6    vga_arbiter
ecryptfs         mem                 snd       tty26  tty49  ttyS12     ttyS7    vhci
fb0              memory_bandwidth    sr0       tty27  tty5   ttyS13     ttyS8    vhost-net
fd               midi                stderr    tty28  tty50  ttyS14     ttyS9    vmci
full             mqueue              stdin     tty29  tty51  ttyS15     uhid     vsock
fuse             net                 stdout    tty3   tty52  ttyS16     uinput   zero
hidraw0          network_latency     tty       tty30  tty53  ttyS17     urandom
hpet             network_throughput  tty0      tty31  tty54  ttyS18     userio
hugepages        null                tty1      tty32  tty55  ttyS19     vcs
```

Actually they are files and pointers to the under laying device hardware. Try`ls -l` to see that .

There are some common device names in .in linux World :

```
Name    Device
cdrom    CD drive
console    Special entry for the currently used console.
cua*    Serial ports
dsp*    Devices for sampling and recording
fd*    Entries for most kinds of floppy drives, the default is /dev/fd0, a floppy drive for 1.44 MB floppies.
hd[a-t][1-16]    Standard support for IDE drives with maximum amount of partitions each.
ir*    Infrared devices
isdn*    Management of ISDN connections
js*    Joystick(s)
lp*    Printers
mem    Memory
midi*    midi player
mixer* and music    Idealized model of a mixer (combines or adds signals)
modem    Modem
mouse (also msmouse, logimouse, psmouse, input/mice, psaux)    All kinds of mouses
null    Bottomless garbage can
par*    Entries for parallel port support
pty*    Pseudo terminals
radio*    For Radio Amateurs (HAMs).
ram*    boot device
sd*    SCSI disks with their partitions
sequencer    For audio applications using the synthesizer features of the sound card (MIDI-device controller)
tty*    Virtual consoles simulating vt100 terminals.
usb*    USB card and scanner
video*    For use with a graphics card supporting video.
```

with the special thanks of udev (as a Hardware Abstraction Layer) and the names it provides.

{% hint style="info" %}
### /sys vs /dev

* The /sys filesystem (sysfs) contains files that provide information about devices: whether it's powered on, the vendor name and model, what bus the device is plugged into, etc. It's of interest to applications that manage devices.
* The /dev filesystem contains files that allow programs to access the devices themselves: write data to a serial port, read a hard disk, etc. It's of interest to applications that access devices.

A metaphor is that /sys provides access to the packaging, while /dev provides access to the content of the box.

The reason for /dev existing independently of /sys is partly historical: /dev dates back to the dawn of Unix, while /sys is a much more recent invention. If Linux was designed today with no historical background, /dev/sda might be /sys/block/sda/content.
{% endhint %}

{% hint style="success" %}
#### pesudo File Systems

'Pseudo-' means false, pretend. So "pseudo-filesystem" means a filesystem that doesn't have actual files – rather, it has virtual entries that the filesystem itself makes up on the spot.

/dev, /proc and /sys are virtual "pseudo-filesystems" (not existing on harddisk, but only in RAM – so they do not consume any harddisk space and are completely created on boot).
{% endhint %}

### dbus

D-Bus is a message bus system, a simple way for applications to talk to one another. Beside all of dbus benefits it can read information form /dev folder and relate them with user desktop programs using signals. In fact dbus make a kind of middle layer which keeps programs a way from difficulties of dealing with /dev and /sys directories.

**Notice : **udev and dbus can work in all distributions because sysfs has made required information standardize.

From the administrative perspective there are some ls utilities ( lsusb, lspci , ... ) to show more information about the hardware which has been attached to our system. Lets take a quick look at them:

### lsusb

The lsusb command allows you to display information about USB buses and devices that are attached to them.

```
root@ubuntu16-1:~# lsusb
Bus 001 Device 002: ID 0951:1625 Kingston Technology DataTraveler 101 II
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 002 Device 003: ID 0e0f:0002 VMware, Inc. Virtual USB Hub
Bus 002 Device 002: ID 0e0f:0003 VMware, Inc. Virtual Mouse
Bus 002 Device 001: ID 1d6b:0001 Linux Foundation 1.1 root hub
```

lsusb has some options:

```
root@ubuntu16-1:~# lsusb --help
Usage: lsusb [options]...
List USB devices
  -v, --verbose
      Increase verbosity (show descriptors)
  -s [[bus]:][devnum]
      Show only devices with specified device and/or
      bus numbers (in decimal)
  -d vendor:[product]
      Show only devices with the specified vendor and
      product ID numbers (in hexadecimal)
  -D device
      Selects which device lsusb will examine
  -t, --tree
      Dump the physical USB device hierarchy as a tree
  -V, --version
      Show version of program
  -h, --help
      Show usage and help
```

We can also use the `-v` command-line option to display more verbose output:

```
root@ubuntu16-1:~# lsusb -v

[output truncated]
Bus 001 Device 002: ID 0951:1625 Kingston Technology DataTraveler 101 II
Device Descriptor:
  bLength                18
  bDescriptorType         1
  bcdUSB               2.00
  bDeviceClass            0 (Defined at Interface level)
  bDeviceSubClass         0 
  bDeviceProtocol         0 
  bMaxPacketSize0        64
  idVendor           0x0951 Kingston Technology
  idProduct          0x1625 DataTraveler 101 II
  bcdDevice            1.10
  iManufacturer           1 Kingston
  iProduct                2 DT 101 II
  iSerial                 3 0013729982D5B97196320049
  bNumConfigurations      1
  Configuration Descriptor:
    bLength                 9
    bDescriptorType         2
    wTotalLength           32
    bNumInterfaces          1
    bConfigurationValue     1
    iConfiguration          0 
    bmAttributes         0x80
      (Bus Powered)
    MaxPower              300mA
[output truncated]
```

`-t` tells lsusb to dump the physical USB device hierarchy as a tree. This overrides the v option.

```
root@ubuntu16-1:~# lsusb -t
/:  Bus 02.Port 1: Dev 1, Class=root_hub, Driver=uhci_hcd/2p, 12M
    |__ Port 1: Dev 2, If 0, Class=Human Interface Device, Driver=usbhid, 12M
    |__ Port 2: Dev 3, If 0, Class=Hub, Driver=hub/7p, 12M
/:  Bus 01.Port 1: Dev 1, Class=root_hub, Driver=ehci-pci/6p, 480M
    |__ Port 1: Dev 2, If 0, Class=Mass Storage, Driver=usb-storage, 480M
```

`-V`or `--version` Print version information on standard output, then exit successfully.

```
root@ubuntu16-1:~# lsusb -V
lsusb (usbutils) 007
```

try `usb-devices` , it will give us more detailed info.

### lscpu

lscpu reports information about the cpu and processing units. It does not have any further options or functionality.

```
root@ubuntu16-1:~# lscpu
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                1
On-line CPU(s) list:   0
Thread(s) per core:    1
Core(s) per socket:    1
Socket(s):             1
NUMA node(s):          1
Vendor ID:             GenuineIntel
CPU family:            6
Model:                 142
Model name:            Intel(R) Core(TM) i7-7500U CPU @ 2.70GHz
Stepping:              9
CPU MHz:               2904.002
BogoMIPS:              5808.00
Hypervisor vendor:     VMware
Virtualization type:   full
L1d cache:             32K
L1i cache:             32K
L2 cache:              256K
L3 cache:              4096K
NUMA node0 CPU(s):     0
Flags:                 fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ss syscall nx pdpe1gb rdtscp lm constant_tsc arch_perfmon nopl xtopology tsc_reliable nonstop_tsc pni pclmulqdq ssse3 fma cx16 pcid sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand hypervisor lahf_lm abm 3dnowprefetch fsgsbase tsc_adjust bmi1 avx2 smep bmi2 invpcid mpx rdseed adx smap clflushopt xsaveopt xsavec xsaves arat
```

### lshw

lshw is a general purpose utility, that reports detailed and brief information about multiple different hardware units such as cpu, memory, disk, usb controllers, network adapters etc. Lshw extracts the information from different /proc files.

Do remember that the lshw command executed by root (superuser):

```
root@ubuntu16-1:~# lshw --help
Hardware Lister (lshw) - B.02.17
usage: lshw [-format] [-options ...]
       lshw -version

    -version        print program version (B.02.17)

format can be
    -html           output hardware tree as HTML
    -xml            output hardware tree as XML
    -short          output hardware paths
    -businfo        output bus information

options can be
    -class CLASS    only show a certain class of hardware
    -C CLASS        same as '-class CLASS'
    -c CLASS        same as '-class CLASS'
    -disable TEST   disable a test (like pci, isapnp, cpuid, etc. )
    -enable TEST    enable a test (like pci, isapnp, cpuid, etc. )
    -quiet          don't display status
    -sanitize       sanitize output (remove sensitive information like serial numbers, etc.)
    -numeric        output numeric IDs (for PCI, USB, etc.)
```

Lets try lshw -short :

```
root@ubuntu16-1:~# lshw -short
H/W path             Device      Class       Description
========================================================
                                 system      VMware Virtual Platform
/0                               bus         440BX Desktop Reference Platform
/0/0                             memory      86KiB BIOS
/0/4                             processor   Intel(R) Core(TM) i7-7500U CPU @ 2.70GHz
/0/4/94                          memory      16KiB L1 cache
/0/5                             processor   CPU
/0/5/95                          memory      16KiB L1 cache
/0/6                             processor   CPU
/0/6/96                          memory      16KiB L1 cache
/0/7                             processor   CPU
/0/7/97                          memory      16KiB L1 cache
/0/8                             processor   CPU
/0/8/98                          memory      16KiB L1 cache
/0/9                             processor   CPU
/0/9/99                          memory      16KiB L1 cache
/0/a                             processor   CPU

[output truncated]
```

### lspci

lspci is a utility for displaying information about PCI buses in the system and devices connected to them.By default, it shows a brief list of devices.

```
root@ubuntu16-1:~# lspci
00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (rev 01)
00:01.0 PCI bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX AGP bridge (rev 01)
00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 08)
00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 08)
00:07.7 System peripheral: VMware Virtual Machine Communication Interface (rev 10)
00:0f.0 VGA compatible controller: VMware SVGA II Adapter
00:10.0 SCSI storage controller: LSI Logic / Symbios Logic 53c1030 PCI-X Fusion-MPT Dual Ultra320 SCSI (rev 01)
00:11.0 PCI bridge: VMware PCI bridge (rev 02)
00:15.0 PCI bridge: VMware PCI Express Root Port (rev 01)
00:15.1 PCI bridge: VMware PCI Express Root Port (rev 01)

[output truncated]

00:18.7 PCI bridge: VMware PCI Express Root Port (rev 01)
02:00.0 USB controller: VMware USB1.1 UHCI Controller
02:01.0 Ethernet controller: Intel Corporation 82545EM Gigabit Ethernet Controller (Copper) (rev 01)
02:02.0 Multimedia audio controller: Ensoniq ES1371 / Creative Labs CT2518/ES1373 (rev 02)
02:03.0 USB controller: VMware USB2 EHCI Controller
02:05.0 SATA controller: VMware SATA AHCI controller
```

All of lspci switches:

```
root@ubuntu16-1:~# lspci --help
lspci: invalid option -- '-'
Usage: lspci [<switches>]

Basic display modes:
-mm        Produce machine-readable output (single -m for an obsolete format)
-t        Show bus tree

Display options:
-v        Be verbose (-vv for very verbose)
-k        Show kernel drivers handling each device
-x        Show hex-dump of the standard part of the config space
-xxx        Show hex-dump of the whole config space (dangerous; root only)
-xxxx        Show hex-dump of the 4096-byte extended config space (root only)
-b        Bus-centric view (addresses and IRQ's as seen by the bus)
-D        Always show domain numbers

Resolving of device ID's to names:
-n        Show numeric ID's
-nn        Show both textual and numeric ID's (names & numbers)
-q        Query the PCI ID database for unknown ID's via DNS
-qq        As above, but re-query locally cached entries
-Q        Query the PCI ID database for all ID's via DNS

Selection of devices:
-s [[[[<domain>]:]<bus>]:][<slot>][.[<func>]]    Show only devices in selected slots
-d [<vendor>]:[<device>][:<class>]        Show only devices with specified ID's

Other options:
-i <file>    Use specified ID database instead of /usr/share/misc/pci.ids.gz
-p <file>    Look up kernel modules in a given file instead of default modules.pcimap
-M        Enable `bus mapping' mode (dangerous; root only)

PCI access options:
-A <method>    Use the specified PCI access method (see `-A help' for a list)
-O <par>=<val>    Set PCI access parameter (see `-O help' for a list)
-G        Enable PCI access debugging
-H <mode>    Use direct hardware access (<mode> = 1 or 2)
-F <file>    Read PCI configuration dump from a given file
```

The `-t` option will display the output in tree format with information about bus, and how devices are connected to those buses. The output will be only using the numerical ids:

```
root@ubuntu16-1:~# lspci -t
-[0000:00]-+-00.0
           +-01.0-[01]--
           +-07.0
           +-07.1
           +-07.3
           +-07.7
           +-0f.0
           +-10.0
           +-11.0-[02]--+-00.0
           |            +-01.0
           |            +-02.0
           |            +-03.0
           |            \-05.0
           +-15.0-[03]--
           +-15.1-[04]--
           +-15.2-[05]--
           +-15.3-[06]--
           +-15.4-[07]--
           +-15.5-[08]--

           [output truncated]

           +-18.3-[1e]--
           +-18.4-[1f]--
           +-18.5-[20]--
           +-18.6-[21]--
           \-18.7-[22]--
```

lspci has a very helpful switch to know the name of the kernel module that will be handling the operations of a particular device. (this option will work only on Kernel 2.6 version and above):

```
root@ubuntu16-1:~# lspci -k
00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (rev 01)
    Subsystem: VMware Virtual Machine Chipset
    Kernel driver in use: agpgart-intel
00:01.0 PCI bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX AGP bridge (rev 01)
    Kernel modules: shpchp
00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 08)
    Subsystem: VMware Virtual Machine Chipset
00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
    Subsystem: VMware Virtual Machine Chipset
    Kernel driver in use: ata_piix
    Kernel modules: pata_acpi
00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 08)
    Subsystem: VMware Virtual Machine Chipset
    Kernel modules: i2c_piix4
00:07.7 System peripheral: VMware Virtual Machine Communication Interface (rev 10)
    Subsystem: VMware Virtual Machine Communication Interface
    Kernel driver in use: vmw_vmci
    Kernel modules: vmw_vmci
00:0f.0 VGA compatible controller: VMware SVGA II Adapter
    Subsystem: VMware SVGA II Adapter
    Kernel driver in use: vmwgfx
    Kernel modules: vmwgfx
00:10.0 SCSI storage controller: LSI Logic / Symbios Logic 53c1030 PCI-X Fusion-MPT Dual Ultra320 SCSI (rev 01)
    Subsystem: VMware LSI Logic Parallel SCSI Controller
    Kernel driver in use: mptspi
    Kernel modules: mptspi
00:11.0 PCI bridge: VMware PCI bridge (rev 02)
00:15.0 PCI bridge: VMware PCI Express Root Port (rev 01)
    Kernel driver in use: pcieport
    Kernel modules: shpchp
00:15.1 PCI bridge: VMware PCI Express Root Port (rev 01)
    Kernel driver in use: pcieport
    Kernel modules: shpchp
00:15.2 PCI bridge: VMware PCI Express Root Port (rev 01)
    Kernel driver in use: pcieport
    Kernel modules: shpchp

[output truncated]

00:18.6 PCI bridge: VMware PCI Express Root Port (rev 01)
    Kernel driver in use: pcieport
    Kernel modules: shpchp
00:18.7 PCI bridge: VMware PCI Express Root Port (rev 01)
    Kernel driver in use: pcieport
    Kernel modules: shpchp
02:00.0 USB controller: VMware USB1.1 UHCI Controller
    DeviceName: usb
    Subsystem: VMware USB1.1 UHCI Controller
    Kernel driver in use: uhci_hcd
02:01.0 Ethernet controller: Intel Corporation 82545EM Gigabit Ethernet Controller (Copper) (rev 01)
    DeviceName: Ethernet0
    Subsystem: VMware PRO/1000 MT Single Port Adapter
    Kernel driver in use: e1000
    Kernel modules: e1000
02:02.0 Multimedia audio controller: Ensoniq ES1371 / Creative Labs CT2518/ES1373 (rev 02)
    DeviceName: sound
    Subsystem: Ensoniq AudioPCI 64V/128 / Creative CT4810/CT5803/CT5806 [Sound Blaster PCI]
    Kernel driver in use: snd_ens1371
    Kernel modules: snd_ens1371
02:03.0 USB controller: VMware USB2 EHCI Controller
    DeviceName: ehci
    Subsystem: VMware USB2 EHCI Controller
    Kernel driver in use: ehci-pci
02:05.0 SATA controller: VMware SATA AHCI controller
    DeviceName: sata0
    Subsystem: VMware SATA AHCI controller
    Kernel driver in use: ahci
    Kernel modules: ahci
```

lets try a tool in order to see whether these modules have been loaded.

### lsmod

lsmod is a very simple program with no options.

```
root@ubuntu16-1:~# lsmod --help
Usage: lsmod
```

it nicely formats the contents of the file /proc/modules, which contains information about the status of all currently-loaded Linux Kernel Modules (LKMs).

```
root@ubuntu16-1:~# lsmod
Module                  Size  Used by
nls_iso8859_1          16384  1
uas                    24576  0
usb_storage            69632  2 uas
vmw_vsock_vmci_transport    28672  2
vsock                  36864  3 vmw_vsock_vmci_transport
xt_multiport           16384  1
iptable_filter         16384  1
ip_tables              24576  1 iptable_filter
x_tables               36864  3 xt_multiport,ip_tables,iptable_filter
crct10dif_pclmul       16384  0
crc32_pclmul           16384  0
vmw_balloon            20480  0
ghash_clmulni_intel    16384  0
pcbc                   16384  0
snd_ens1371            28672  2
snd_ac97_codec        131072  1 snd_ens1371
aesni_intel           167936  0
gameport               16384  1 snd_ens1371
ac97_bus               16384  1 snd_ac97_codec
aes_x86_64             20480  1 aesni_intel
crypto_simd            16384  1 aesni_intel
snd_pcm               102400  2 snd_ac97_codec,snd_ens1371
glue_helper            16384  1 aesni_intel
cryptd                 24576  3 crypto_simd,ghash_clmulni_intel,aesni_intel
snd_seq_midi           16384  0
snd_seq_midi_event     16384  1 snd_seq_midi
input_leds             16384  0
snd_rawmidi            32768  2 snd_seq_midi,snd_ens1371
joydev                 20480  0
serio_raw              16384  0
snd_seq                65536  2 snd_seq_midi_event,snd_seq_midi
snd_seq_device         16384  3 snd_seq,snd_rawmidi,snd_seq_midi
snd_timer              32768  2 snd_seq,snd_pcm
snd                    77824  11 snd_seq,snd_ac97_codec,snd_timer,snd_rawmidi,snd_ens1371,snd_seq_device,snd_pcm
soundcore              16384  1 snd
nfit                   49152  0
i2c_piix4              24576  0
shpchp                 36864  0
vmw_vmci               69632  2 vmw_balloon,vmw_vsock_vmci_transport
mac_hid                16384  0
binfmt_misc            20480  1
parport_pc             32768  0
ppdev                  20480  0
lp                     20480  0
parport                49152  3 lp,parport_pc,ppdev
autofs4                40960  2
vmw_pvscsi             24576  0
vmxnet3                61440  0
hid_generic            16384  0
usbhid                 53248  0
hid                   118784  2 hid_generic,usbhid
vmwgfx                241664  4
ttm                    98304  1 vmwgfx
psmouse               139264  0
drm_kms_helper        151552  1 vmwgfx
syscopyarea            16384  1 drm_kms_helper
sysfillrect            16384  1 drm_kms_helper
sysimgblt              16384  1 drm_kms_helper
fb_sys_fops            16384  1 drm_kms_helper
mptspi                 24576  2
ahci                   36864  0
libahci                32768  1 ahci
e1000                 143360  0
drm                   352256  7 vmwgfx,ttm,drm_kms_helper
mptscsih               40960  1 mptspi
mptbase               102400  2 mptscsih,mptspi
scsi_transport_spi     32768  1 mptspi
pata_acpi              16384  0
fjes                   77824  0
```

try `cat /proc/modules` and compare the results.

There is nothing like Drivers in linux and as we said, udev is responsible for calling related module with required information using modprobe.

### modprobe

modprobe intelligently adds or removes a module from the Linux kernel. For demonstration lets remove and add e1000 module which is for Ethernet car on my system:

```
root@ubuntu16-1:~# modprobe -r e1000
root@ubuntu16-1:~# modprobe e1000
```

and we would get disconnected and then connected again. modprobe has a long list of options try`modprobe --help` to see them.

.

.

.

Sources:

[https://www.tldp.org/HOWTO/SCSI-2.4-HOWTO/procfs.html](https://www.tldp.org/HOWTO/SCSI-2.4-HOWTO/procfs.html)

[https://medium.com/@jain.sm/pseudo-file-systems-in-linux-5bf67eb6e450](https://medium.com/@jain.sm/pseudo-file-systems-in-linux-5bf67eb6e450)

[https://stackoverflow.com/questions/16431554/how-devfs-and-dev-file-system-differ](https://stackoverflow.com/questions/16431554/how-devfs-and-dev-file-system-differ)

[https://www.tecmint.com/load-and-unload-kernel-modules-in-linux/](https://www.tecmint.com/load-and-unload-kernel-modules-in-linux/)

[http://dwaves.de/2017/05/29/linux-difference-between-proc-sys-and-dev-sysfs/](http://dwaves.de/2017/05/29/linux-difference-between-proc-sys-and-dev-sysfs/)

[https://www.linuxtopia.org/online_books/introduction_to_linux/linux_The_most_common_devices.html](https://www.linuxtopia.org/online_books/introduction_to_linux/linux_The_most_common_devices.html)

[https://unix.stackexchange.com/questions/176215/difference-between-dev-and-sys](https://unix.stackexchange.com/questions/176215/difference-between-dev-and-sys)

[https://wiki.debian.org/udev](https://wiki.debian.org/udev)

[https://opensource.com/article/16/11/managing-devices-linux](https://opensource.com/article/16/11/managing-devices-linux)

[https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-sysinfo-hardware-lsusb](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-sysinfo-hardware-lsusb)

[https://www.systutorials.com/docs/linux/man/8-lspci/](https://www.systutorials.com/docs/linux/man/8-lspci/)

[https://www.computerhope.com/unix/lsmod.htm](https://www.computerhope.com/unix/lsmod.htm)

[https://www.binarytides.com/linux-commands-hardware-info/](https://www.binarytides.com/linux-commands-hardware-info/)

[https://linux.die.net/man/8/modprobe](https://linux.die.net/man/8/modprobe)
