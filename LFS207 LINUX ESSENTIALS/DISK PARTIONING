# **Learning Objectives**

By the end of this chapter, you should be able to:

- Describe and contrast the most common types of hard disks and data buses
- Explain disk geometry and other partitioning concepts
- Understand how disk devices are named and how to identify their associated device nodes
- Use utilities such as **fdisk**, **blkid** and **lsblk**
- Distinguish among and select different partitioning strategies
- Back up and restore partition tables

# **Common Disk Types**

There are a number of different hard disk types, each of which is characterized by the type of data bus they are attached to, and other factors such as speed, capacity, how well multiple drives work simultaneously, etc.

**Disk Types**

SATA (Serial Advanced Technology Attachment)
SATA disks were designed to replace the old IDE drives. They offer a smaller cable size (7 pins), native hot swapping, and faster and more efficient data transfer. They are seen as SCSI devices.

SCSI (Small Computer Systems Interface)
SCSI disks range from narrow (8 bit bus) to wide (16 bit bus), with a transfer rate from about 5 MB per second (narrow, standard SCSI) to about 160 MB per second (Ultra-Wide SCSI-3). SCSI has numerous versions such as Fast, Wide, and Ultra, Ultrawide.

SAS (Serial Attached SCSI)

SAS uses a newer point-to-point serial protocol, has a better performance than SATA disks and is better suited for servers. See the *["SAS vs SATA: What's the Difference"](https://www.hp.com/us-en/shop/tech-takes/sas-vs-sata)* article by Zach Cabading to learn more.

USB (Universal Serial Bus)
These include flash drives and floppies. And are seen as SCSI devices.

SSD (Solid State Drives)
Modern SSD drives have come down in price, have no moving parts, use less power than drives with rotational media, and have faster transfer speeds. Internal SSDs are even installed with the same form factor and in the same enclosures as conventional drives. SSDs still cost a bit more, but price is decreasing. It is common to have both SSDs and rotational drives in the same machines, with frequently accessed and performance critical data transfers taking place on the SSDs.

IDE and EIDE (Integrated Drive Electronics, Enhanced IDE)
These are obsolete.

# **Disk Geometry**

Rotational disks are composed of one or more platters and each platter is read by one or more heads. Heads read a circular track off a platter as the disk spins. Circular tracks are divided into data blocks called sectors. Historically, disks were manufactured with sectors of 512 bytes; 4 KB is now most common by far; larger sector sizes can lead to faster transfer speeds. Linux still uses a logical sector size of 512 bytes for backward compatibility, but this is simply for pretend in software. A cylinder is a group which consists of the same track on all platters.

The physical structural image has become less and less relevant as internal electronics on the drive actually obscure much of it. Furthermore, SSDs have no moving parts or anything like the above ingredients, and for SSDs these geometry concepts make no sense.

Command:

**$ sudo fdisk -l /dev/sdc |grep -i sector**

Output:

**Disk /dev/sdc: 1.8 TiB, 2000398934016 bytes, 3907029168 sectorsUnits: sectors of 1 * 512 = 512 bytesSector size (logical/physical): 512 bytes / 4096 bytes**

We can see the geometry with **fdisk**:

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/b54kl5wlsyuj-fdisk-pic.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/b54kl5wlsyuj-fdisk-pic.png)

**Screenshot of the sudo fdisk -l /dev/sda command and its output**

Note the use of the **-l** option which simply lists the partition table without entering interactive mode.

# **Partition Organization**

Disks are divided into partitions. A partition is a physically contiguous region on the disk. On the most common architectures, there are two partitioning schemes in use:

- MBR (Master Boot Record)
- GPT (GUID Partition Table)

MBR dates back to the early days of MSDOS. When using MBR, a disk may have up to four primary partitions. One of the primary partitions can be designated as an extended partition, which can be subdivided further into logical partitions with 15 partitions possible.

When using the MBR scheme, if we have a SCSI, for example, **/dev/sda**, then **/dev/sda1** is the first primary partition and **/dev/sda2** is the second primary partition. If we created an extended partition **/dev/sda3**, it could be divided into logical partitions. All partitions greater than four are logical partitions (meaning contained within an extended partition). There can only be one extended partition, but it can be divided into numerous logical partitions.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/lxgjxy1ovhr4-Course_WarningBanners_Blank_regular4.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/lxgjxy1ovhr4-Course_WarningBanners_Blank_regular4.png)

GPT is on all modern systems and is based on UEFI (Unified Extensible Firmware Interface). By default, it may have up to 128 primary partitions. When using the GPT scheme, there is no need for extended partitions. Partitions can be up to 233 TB in size (with MBR, the limit is just 2TB).

# **Why Partition?**

There are multiple reasons as to why it makes sense to divide your system data into multiple partitions, including:

- Separation of user and application data from operating system files
- Sharing between operating systems and/or machines
- Security enhancement by imposing different quotas and permissions for different system parts
- Size concerns; keeping variable and volatile storage isolated from stable
- Performance enhancement of putting most frequently used data on faster storage media
- Swap space can be isolated from data and also used for hibernation storage.

Deciding what to partition and how to separate your partitions is cause for thought. The reasons to have distinct partitions include increased granularity of security, quota, settings or size restrictions. You could have distinct partitions to allow for data protection.

A common partition layout contains a **/boot** partition, a partition for the root filesystem **/**, a swap partition, and a partition for the **/home** directory tree.

Keep in mind that it is more difficult to resize a partition after the fact than during install/creation time. Plan accordingly.

# **GPT Partition Table**

Modern hardware comes with GPT support; MBR support will gradually fade away.

The Protective MBR is for backwards compatibility, so UEFI systems can be booted the old way.

There are two copies of the GPT header, at the beginning and at the end of the disk, describing metadata:

- List of usable blocks on disk
- Number of partitions
- Size of partition entries. Each partition entry has a minimum size of 128 bytes.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/u9ypj1wzvskx-GPTLayout.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/u9ypj1wzvskx-GPTLayout.png)

**GPT Disk Partition Table**

The **blkid** utility (to be discussed later) shows information about partitions.

On a modern UEFI/GPT system run the following command:

**$ sudo blkid /dev/sda8**

Output:

**/dev/sda8: LABEL="RHEL8" UUID="53ea9807-fd58-4433-9460-d03ec36f73a3" BLOCK_SIZE="4096" TYPE="ext4"↪ PARTUUID="0c79e35b-e58b-4ce3-bd34-45651b01cf43"**

On a legacy MBR system use this command:

**$ sudo blkid /dev/sdb2**

Output:

**/dev/sdb2: LABEL="RHEL8" UUID="6921b738-1e36-429a-89be-8b97cf2f0556" BLOCK_SIZE="4096" TYPE="ext4"↪ PARTUUID="00022650-02"**

Note both examples give a unique UUID, which describes the filesystem on the partition, not the partition itself. It changes if the filesystem is reformatted.

The GPT partition also gives a PARTUUID which describes the partition and stays the same even if the filesystem is reformatted. If the hardware supports it, it is possible to migrate an MBR system to GPT, but it is not hard to brick the machine while doing so. Thus, usually the benefits are not worth the risk.

# **Naming Disk Devices and Device Nodes**

The Linux kernel interacts at a low level with disks through device nodes normally found in the **/dev** directory. Normally, device nodes are accessed only through the infrastructure of the kernel's Virtual File System; raw access through the device nodes is an extremely efficient way to destroy a filesystem.

For an example of proper raw access, you can format a partition, as in this command:

**$ sudo mkfs.ext4 /dev/sda9**

Device nodes for SCSI and SATA disks follow a simple **xxy[z]** naming convention, where **xx** is the device type (usually **sd**), **y** is the letter for the drive number (**a**, **b**, **c**, etc.), and **z** is the partition number:

- The first hard disk is **/dev/sda**
- The second hard disk is **/dev/sdb**
- Etc.

Partitions are also easily enumerated, as in:

- **/dev/sdb1** is the first partition on the second disk
- **/dev/sdc4** is the fourth partition on the third disk.

In the above, sd means SCSI or SATA disk. Back in the days where IDE disks could be found, they would have been **/dev/hda3**, **/dev/hdb**, etc.

Doing **ls -l /dev** will show you the current available disk device nodes.

# **blkid**

**blkid** is a utility to locate block devices and report on their attributes. It works with the **libblkid** library. It can take as an argument a particular device or list of devices. The screenshot below shows a use of **blkid** with arguments.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/qrqxgmjyoc32-blkid.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/qrqxgmjyoc32-blkid.png)

**Screenshot of the sudo blkid /dev/sda* command and its output**

It can determine the type of content (e.g. filesystem, swap) a block device holds, and also attributes (tokens, **NAME=value** pairs) from the content metadata (e.g., **LABEL** or **UUID** fields).

**blkid** will only work on devices which contain data that is finger-printable: e.g., an empty partition will not generate a block-identifying UUID. **blkid** has two main forms of operation: either searching for a device with a specific **NAME=value** pair, or displaying **NAME=value** pairs for one or more devices. Without arguments, it will report on all devices. There are quite a few options designating how to specify devices and what attributes to report on. Other sample commands:

**$ sudo blkid**

**$ sudo blkid /dev/sda***

**$ sudo blkid -L root**

# **lsblk**

A related utility is **lsblk** which presents block device information in a tree format, as in the following screenshot.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/a4yzp7kzk33y-lsblk.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/a4yzp7kzk33y-lsblk.png)

**Screenshot of the lsblk command and its output**

Command:

**$ lsblk -h**

Output:

**Usage: lsblk [options] [<device> ...]**

**List information about block devices.**

**Options: -a, --all print all devices -b, --bytes print SIZE in bytes rather than in human readable format -d, --nodeps don't print slaves or holders -D, --discard print discard capabilities -z, --zoned print zone model -e, --exclude <list> exclude devices by major number (default: RAM disks) -f, --fs output info about filesystems -i, --ascii use ascii characters only -I, --include <list> show only devices with specified major numbers -J, --json use JSON output format -l, --list use list format output -T, --tree use tree format output -m, --perms output info about permissions -n, --noheadings don't print headings -o, --output <list> output columns -O, --output-all output all columns -p, --paths print complete device path -P, --pairs use key="value" output format -r, --raw use raw output format -s, --inverse inverse dependencies -S, --scsi output info about SCSI devices -t, --topology output info about topology -x, --sort <column> sort output by <column>**

**-h, --help display this help -V, --version display version**

**Available output columns:......For more details see lsblk(8).**

# **Sizing Up Partitions**

Most Linux systems should use a minimum of two partitions.

- root (**/**) is used for the filesystem. Most installations will have more than one filesystem on more than one partition, which are joined together at *mount points*. It is difficult with most filesystem types to resize the root partition, but using LVM (discussed later) can make this easier. While it is certainly possible to run Linux with just the root partition, most systems use more partitions to allow for easier backups, more efficient use of disk drives, and better security.
- Swap is used as an extension of physical memory. The usual recommendation is swap size should be equal to physical memory in size; sometimes twice that is recommended. However, the correct choice depends on the related issues of system use scenarios as well as hardware capabilities. Adding more and more swap will not necessarily help because at a certain point it becomes useless. One will need to either add more memory or re-evaluate the system setup.

On older rotational hard drive media, it may make more sense to have a separate swap partition, but on SSD-type media, this is unimportant. However, one still may want to put swap on slower and probably cheaper hardware. This is true whether you use a partition or a file, which is becoming a more prevalent choice.

Some distributions, including Ubuntu, default to use of a swap file rather than a partition:

- It is more flexible (resizing is easier, for example)
- It can be more dangerous, however, if error or bug spreads corruption

Note that some distributions are now using (optionally) **[zram](https://www.kernel.org/doc/html/latest/admin-guide/blockdev/zram.html),** which instead of using disk storage for swap, uses compressed memory. This can easily lead to out of memory conditions, but in expert hands, it can improve performance.

# **Backing Up and Restoring Partition Tables**

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/kr6dpq33m029-Course_WarningBanners_Blank_regular5.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/kr6dpq33m029-Course_WarningBanners_Blank_regular5.png)

For MBR systems, **dd** can be used for converting and copying files. However, be careful using **dd**: a simple typing error or misused option could destroy your entire disk.

The following command will back up the MBR (along with the partition table):

**$ dd if=/dev/sda of=mbrbackup bs=512 count=1**

The MBR can be restored using the following command:

**$ sudo dd if=mbrbackup of=/dev/sda bs=512 count=1**

The above **dd** commands only copy the primary partition table; they do not deal with any partition tables stored in the other partitions (for extended partitions, etc.).

For GPT systems it is best to use the **sgdisk** tool, as in this command:

**x8:/tmp>sudo sgdisk -p /dev/sda**

Output:

**Disk /dev/sda: 1000215216 sectors, 476.9 GiBModel: SAMSUNG MZNLN512Sector size (logical/physical): 512/512 bytesDisk identifier (GUID): DBDBE747-8392-4B62-97AA-6214490073DCPartition table holds up to 128 entries....Number Start (sector) End (sector) Size      Code  Name   1           2048        534527 260.0 MiB  EF00  EFI System Partition   2         534528        567295 16.0 MiB   0C01  Microsoft reserved .......**

Note if run on a pure MBR system, the output is different:

**c8:/tmp>sudo sgdisk -p /dev/sda**

Output:

- ****************************************************************Found invalid GPT and valid MBR; converting MBR to GPT formatin memory.**************************************************************Disk /dev/sda: 500118192 sectors, 238.5 GiBModel: Crucial_CT256MX1Sector size (logical/physical): 512/4096 bytes....**

# **Partition Table Editors**

There are a number of utilities which can be used to manage partition tables.

**fdisk** is a menu-driven partition table editor. It is the most standard and one of the most flexible of the partition table editors. As with any partition table editor, make sure that you either write down the current partition table settings or make a copy of the current settings before making changes.

The **sfdisk** command is a non-interactive Linux-based partition editor program, making it useful for scripting. Use the **sfdisk** tool with care.

For GPT systems, **gdisk** and **sgdisk** play a similar role. Both also work on MBR systems.

**parted** is the GNU partition manipulation program. It can create, remove, resize, and move partitions (including certain filesystems). The GUI interface to the **parted** command is **gparted**.

Many Linux distributions have a live/installation version which can be run off either a CDROM or USB stick, which include a copy of **gparted**, so they can easily be used as a graphical partitioning tool on disks which are not actually being used while the partitioning program is run.

# **Using fdisk**

**fdisk** is part of the base Linux installation, so it is a good idea to learn how to use it. You must be root to run **fdisk**. It can be somewhat complex to handle, and caution is advised. The **fdisk** interface is simple and text-menu driven. After starting on a particular disk, as in this command:

**$ sudo fdisk /dev/sdb**

you can issue the one-letter commands itemized below:

- **m**: Display the menu
- **p**: List the partition table
- **n**: Create a new partition
- **d**: Delete a partition
- **t**: Change a partition type
- **w**: Write the new partition table information and exit
- **q**: Quit without making changes

Fortunately, no actual changes are made until you write the partition table to the disk by entering **w**. It is therefore important to verify your partition table is correct (with **p**) before writing to disk with **w**. If something is wrong, you can jump out safely with **q**.

The system will not use the new partition table until you reboot. However, you can use the following command:

**$ sudo partprobe -s**

to try and read in the revised partition table. However, this doesn't always work reliably and it is best to reboot before doing things like formatting new partitions, etc., as mixing up partitions can be catastrophic.

At any time you can run the following command:

**$ cat /proc/partitions**

to examine what partitions the operating system is currently aware of.

You can display the partition table and take no action with the following command:

**$ sudo fdisk -l /dev/sda**