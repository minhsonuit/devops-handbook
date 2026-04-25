# Linux CLI Basics

> Ngay bat dau: ___

## Navigation

```bash
pwd                    # Thu muc hien tai
ls -la                 # List chi tiet, bao gom hidden files
ls -lah                # Them human-readable size
cd /var/log            # Di chuyen tuyet doi
cd ..                  # Di len 1 cap
cd -                   # Quay lai thu muc truoc do
tree -L 2              # Xem cay thu muc 2 cap
```

## File operations

```bash
# Tao
touch file.txt
mkdir -p path/to/dir          # Tao ca parent directories
cp -r source/ dest/           # Copy thu muc
mv old.txt new.txt            # Doi ten hoac di chuyen
rm -rf dir/                   # Xoa thu muc (can than!)

# Xem noi dung
cat file.txt                  # Xem toan bo
head -n 20 file.txt           # 20 dong dau
tail -n 20 file.txt           # 20 dong cuoi
tail -f /var/log/syslog       # Follow log realtime
less file.txt                 # Xem tung trang (q de thoat)
wc -l file.txt                # Dem so dong
```

## Text processing

```bash
# Grep — tim kiem trong file
grep "error" /var/log/syslog
grep -i "error" file.txt              # Khong phan biet hoa thuong
grep -r "TODO" /path/to/project/      # Tim trong tat ca file con
grep -n "pattern" file.txt            # Hien thi so dong
grep -c "error" file.txt              # Dem so dong match
grep -v "debug" file.txt              # Loai bo dong chua "debug"

# Awk — xu ly cot du lieu
awk '{print $1, $4}' access.log       # In cot 1 va 4
awk -F: '{print $1}' /etc/passwd      # Tach theo dau :

# Sed — find & replace
sed 's/old/new/g' file.txt            # Replace tat ca
sed -i 's/old/new/g' file.txt         # Replace truc tiep trong file
sed -n '10,20p' file.txt              # In dong 10 den 20

# Sort & Unique
sort file.txt                         # Sap xep
sort -n file.txt                      # Sap xep theo so
sort file.txt | uniq -c               # Dem so lan xuat hien
```

## Pipes va Redirection

```bash
# Pipe: noi output cua lenh nay vao input lenh kia
cat file.txt | grep "error" | wc -l

# Redirect output
command > file.txt              # Ghi de
command >> file.txt             # Ghi them
command 2> error.log            # Chi redirect stderr
command > output.log 2>&1       # Ca stdout va stderr

# /dev/null — "thung rac"
command > /dev/null 2>&1        # Bo toan bo output
```

## Tim kiem file

```bash
# Find
find / -name "nginx.conf"                    # Tim theo ten
find /var/log -name "*.log" -mtime -1         # Log thay doi trong 1 ngay
find / -type f -size +100M                    # File lon hon 100MB
find /tmp -type f -mtime +7 -delete           # Xoa file cu hon 7 ngay

# Which / Where
which nginx                # Duong dan binary
whereis nginx              # Binary + man + source
```

## System info nhanh

```bash
hostname                   # Ten may
uname -a                   # Kernel info
uptime                     # Thoi gian chay
date                       # Ngay gio
whoami                     # User hien tai
id                         # UID, GID, groups
cat /etc/os-release        # Distro info
free -h                    # RAM
df -h                      # Disk
nproc                      # So CPU cores
```

## Tar / Zip

```bash
# Nen
tar -czf archive.tar.gz /path/to/dir    # Nen bang gzip
tar -cjf archive.tar.bz2 /path/to/dir   # Nen bang bzip2

# Giai nen
tar -xzf archive.tar.gz                 # Giai nen gzip
tar -xzf archive.tar.gz -C /dest/       # Giai nen vao thu muc

# Zip
zip -r archive.zip /path/to/dir
unzip archive.zip -d /dest/
```

## Meo thuc dung

```bash
# Chay lai lenh cuoi cung
!!

# Chay lai lenh cuoi bat dau bang "docker"
!docker

# Tim trong history
history | grep "docker"
Ctrl + R                 # Reverse search

# Alias
alias ll='ls -la'
alias dps='docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"'
```
