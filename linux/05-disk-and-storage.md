# Linux Disk & Storage

> Ngay bat dau: ___

## Xem disk usage

```bash
# Tong quan partitions
df -h                          # Human-readable
df -h /                        # Chi xem root partition
df -hT                         # Them filesystem type

# Dung luong thu muc
du -sh /var/log
du -sh /var/log/* | sort -h    # Sort theo size
du -sh /var/log/* | sort -h | tail -10  # 10 thu muc lon nhat

# Tim file lon
find / -type f -size +100M -exec ls -lh {} \; 2>/dev/null
find /var -type f -size +50M | head -20
```

## Mount

```bash
# Xem mount hien tai
mount
mount | grep /dev/sd

# Mount partition
mount /dev/sdb1 /mnt/data

# Unmount
umount /mnt/data

# Auto mount (sua /etc/fstab)
# /dev/sdb1  /mnt/data  ext4  defaults  0  2
```

## LVM co ban

```bash
# Xem volume groups
vgdisplay
vgs

# Xem logical volumes
lvdisplay
lvs

# Mo rong LV
lvextend -L +10G /dev/vg0/lv_data
resize2fs /dev/vg0/lv_data     # ext4
xfs_growfs /dev/vg0/lv_data    # xfs
```

## Don dep disk

```bash
# Xoa log cu
find /var/log -name "*.gz" -mtime +30 -delete
journalctl --vacuum-time=7d    # Giu log 7 ngay
journalctl --vacuum-size=500M  # Gioi han 500MB

# Xoa Docker garbage
docker system df               # Xem Docker dung bao nhieu
docker system prune -a         # Xoa tat ca unused
docker volume prune            # Xoa unused volumes
docker image prune -a          # Xoa unused images

# Xoa package cache
apt clean                      # Debian/Ubuntu
yum clean all                  # CentOS/RHEL
```

## Monitoring disk I/O

```bash
# I/O realtime
iostat -x 1                    # Moi giay
iotop                          # Process nao dang doc/ghi nhieu

# Kiem tra disk health
smartctl -a /dev/sda           # Can cai smartmontools
```
