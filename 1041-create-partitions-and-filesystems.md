# 104.1. Create partitions and filesystems

## **104.1 Create partitions and filesystems**

**Weight:** 2

**Description:** Candidates should be able to configure disk partitions and then create filesystems on media such as hard disks. This includes the handling of swap partitions.

**Key Knowledge Areas:**

* Manage MBR partition tables
* Use various mkfs commands to create various filesystems such as:
* ext2/ext3/ext4
* XFS
* VFAT
* Awareness of ReiserFS and Btrfs
* Basic knowledge of gdisk and parted with GPT

**Terms and Utilities:**

* fdisk
* gdisk
* parted
* mkfs
* mkswap

### BIOS

 The Basic Input/Output System \(BIOS\), \(also known as System BIOS, ROM BIOS \) is a  standard for defining a firmware interface. The BIOS software is built into the PC, and is the first software run by a PC when powered on.

The fundamental purposes of the BIOS are to initialize and test the system hardware components, and to load a bootloader or an operating system from a mass memory device. The BIOS additionally provides abstraction layer for the hardware, i.e. a consistent way for application programs and operating systems to interact with the keyboard, display, and other input/output devices. Variations in the system hardware are hidden by the BIOS from programs that use BIOS services instead of directly accessing the hardware. Modern operating systems ignore the abstraction layer provided by the BIOS and access the hardware components directly. 

### UEFI

The Unified Extensible Firmware Interface \(UEFI\) is a specification that defines a software interface between an operating system and platform firmware. UEFI is meant to replace the Basic Input/Output System \(BIOS\) firmware interface. In practice, most UEFI images provide legacy support for BIOS services. UEFI can support remote diagnostics and repair of computers, even without another operating system.

> The original EFI \(Extensible Firmware Interface\) specification was developed by Intel.

### MBR

A master boot record \(MBR\) is a special type of boot sector at the very beginning of partitioned computer mass storage devices like fixed disks or removable drives. The concept of MBRs was publicly introduced in 1983 with PC DOS 2.0.

The MBR holds the information on how the logical partitions, containing file systems, are organized on that medium. Besides that, the MBR also contains executable code to function as a loader for the installed operating system—usually by passing control over to the loader's second stage. This MBR code is usually referred to as a boot loader.

![](.gitbook/assets/creatpartitionfs-mbr.png)

The organization of the partition table in the MBR limits the maximum addressable storage space of a disk to 2 TB \(232 × 512 bytes\). Therefore, the MBR-based partitioning scheme is in the process of being superseded by the GUID Partition Table \(GPT\) scheme in new computers. A GPT can coexist with an MBR in order to provide some limited form of a backwards compatibility for older systems. 

### GPT

GUID Partition Table \(GPT\) is a standard for the layout of the partition table on a physical hard disk, using globally unique identifiers \(GUID\).

 Although it forms a part of the Unified Extensible Firmware Interface \(UEFI\) standard, it is also used on some BIOS systems because of the limitations of master boot record \(MBR\) partition tables.

### BIOS vs UEFI

* UEFI enables better use of bigger hard drives.Though UEFI is capable of working with the GUID Partition Table \(GPT\) it also supports the traditional master boot record \(MBR\) method of hard drive partitioning.
* UEFI may be faster than the BIOS. Various tweaks and optimizations in the UEFI may help your system boot more quickly it could before.
*  UEFI has room for more useful and usable features than  the BIOS. Among these are cryptography, network authentication, ..., and even a shell environment for running other EFI applications such as diagnostic utilities or flash updates. 
* In addition, both the architecture and the drivers are CPU-independent, which opens the door to a wider variety of processors \(including those using the ARM architecture, for example\).

> UEFI is still not widespread and  major hardware companies have switched over almost exclusively to UEFI use. Many older and less expensive motherboards also still use the BIOS system.

### MBR vs GPT

* Compared with MBR disk, A GPT disk can support larger than 2 TB volumes where MBR cannot. 
* A GPT disk can be basic or dynamic, just like an MBR disk can be basic or dynamic. 
* GPT disks also support up to 128 partitions rather than the 4 primary partitions limited to MBR. 
* Also, GPT keeps a backup of the partition table at the end of the disk. Furthermore, GPT disk provides greater reliability due to replication and cyclical redundancy check \(CRC\) protection of the partition table.
* The GUID partition table \(GPT\) disk partitioning style supports volumes up to 18 exabytes in size and up to 128 partitions per disk, compared to the master boot record \(MBR\) disk partitioning style, which supports volumes up to 2 terabytes in size and up to 4 primary partitions per disk \(or three primary partitions, one extended partition, and unlimited logical drives\). 
* Unlike MBR partitioned disks, data critical to platform operation is located in partitions instead of unpartitioned or hidden sectors. 
* In addition, GPT partitioned disks have redundant primary and backup partition tables for improved partition data structure integrity. 

### MBR vs GPT & BIOS vs UEFI

Usually, MBR and BIOS \(MBR + BIOS\), and GPT and UEFI \(GPT + UEFI\) go hand in hand. This is compulsory for some systems \(eg Windows\), while optional for others \(eg Linux\).







.

.

.

[https://wiki.manjaro.org/index.php?title=Some\_basics\_of\_MBR\_v/s\_GPT\_and\_BIOS\_v/s\_UEFI](https://wiki.manjaro.org/index.php?title=Some_basics_of_MBR_v/s_GPT_and_BIOS_v/s_UEFI)



