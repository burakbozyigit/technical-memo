# LVM Disk Resize
## LVM Disk Increase

```
[root@server html]# fdisk -cu /dev/sda
 
The device presents a logical sector size that is smaller than
the physical sector size. Aligning to a physical sector (or optimal
I/O) size boundary is recommended, or performance may be impacted.
 
 
Command (m for help): d
Partition number (1-4): 2
 
Command (m for help): n
Command action
   e   extended
   p   primary partition (1-4)
p
Partition number (1-4): 2
First sector (1026048-209715199, default 1026048):
Using default value 1026048
Last sector, +sectors or +size{K,M,G} (1026048-209715199, default 209715199):
Using default value 209715199
 
Command (m for help): w
The partition table has been altered!
 
Calling ioctl() to re-read partition table.
 
WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.

[root@server html]# reboot
```

**After reboot**

```
[root@server html]# pvresize /dev/sda2
  Physical volume "/dev/sda2" changed
  1 physical volume(s) resized / 0 physical volume(s) not resized
  
[root@server html]# lvresize -l +100%FREE /dev/vg_volumegroup/lv_root
  Size of logical volume vg_volumegroup/lv_root changed from 8.51 GiB (2178 extents) to 98.51 GiB (25218 extents).
  Logical volume lv_root successfully resized.
```

### Centos <= 6.x



```
[root@server html]# resize2fs /dev/mapper/vg_volumegroup-lv_root
resize2fs 1.41.12 (17-May-2010)
Filesystem at /dev/mapper/vg_volumegroup-lv_root is mounted on /; on-line resizing required
old desc_blocks = 1, new_desc_blocks = 7
Performing an on-line resize of /dev/mapper/vg_volumegroup-lv_root to 25823232 (4k) blocks.
The filesystem on /dev/mapper/vg_volumegroup-lv_root is now 25823232 blocks long.
```
 
### Centos >= 7.x

 
```
[root@server html]# xfs_growfs /dev/mapper/centos-root
meta-data=/dev/mapper/centos-root isize=256    agcount=4, agsize=3276800 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=0        finobt=0
data     =                       bsize=4096   blocks=13107200, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=0
log      =internal               bsize=4096   blocks=6400, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 13107200 to 65552384
```

## LVM Disk Decrease

```Disk decreasing...```

