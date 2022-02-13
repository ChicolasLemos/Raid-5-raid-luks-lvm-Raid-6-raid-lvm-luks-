Raid 5 (RAID-LUKS-LVM)
lsblk to see the disks
```
gdisk /dev/(the location of the disk)
 - then press o and then y
 - next press n and press enter 3 times and use the code fd00
 - press w and save
```
Do that for atleast 3 disks
```
mdadm --create /dev/md0 --level 5 --raid-devices 3 /dev/disk /dev/disk /dev/disk
mdadm --detail /dev/md0
cryptsetup luksFormat --hash=sha512 --key-size=512 --cipher=aes-xts-plain64 --verify-passphrase /dev/md0
cryptsetup luksOpen /dev/md0 md0_crypt
cryptsetup luksDump -v /dev/md0
ls -l /dev/mapper/
pvcreate /dev/mapper/md0_crypt
vgcreate vg0 /dev/mapper/md0_crypt
lvcreate -n lv0 -l +100%FREE vg0
lvdisplay (wait for it to end)
mkfs.xfs /dev/vg0/lv0
cd /mnt/
mkdir (your location for the mount of the disks)
mount /dev/vg0/lv0 /mnt/(your location)
cat /etc/mtab (copy the last phrases)
nano /etc/fstab (paste and add nofail,)
reboot
```
Everytime you enter the machine you need to use the commands
```
cryptsetup luksOpen /dev/md0 md0_crypt
mount -a
```

Raid 6 (RAID-LVM-LUKS) 
lsblk to see the disks
```
gdisk /dev/(the location of the disk)
 - then press o and then y
 - next press n and press enter 3 times and use the code fd00
 - press w and save
```
Do that for atleast 4 disks
```
mdadm --create /dev/md1 --level 6 --raid-devices 4 /dev/disk /dev/disk /dev/disk /dev/disk
mdadm --detail /dev/md1
pvcreate /dev/md1
vgcreate vg1 /dev/md1
lvcreate -n lv1 -l +100%FREE vg1
cryptsetup luksFormat --hash=sha512 --key-size=512 --cipher=aes-xts-plain64 --verify-passphrase /dev/vg1/lv1
cryptsetup luksOpen /dev/vg1/lv1 lv1_crypt
mkfs.xfs /dev/mapper/lv1_crypt
cd /mnt/
mkdir (your location for the mount of the disks)
mount /dev/vg0/lv0 /mnt/(your location)
cat /etc/mtab (copy the last phrases)
nano /etc/fstab (paste and add nofail,)
reboot
```
Everytime you enter the machine you need to use the commands
```
cryptsetup luksOpen /dev/md0 md0_crypt
mount -a
```
