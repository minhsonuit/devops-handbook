# Linux Filesystem & Permissions

> Ngay bat dau: ___

## Cau truc thu muc Linux

```
/
├── etc/          ← Config he thong (nginx.conf, fstab, hosts)
├── var/          ← Du lieu thay doi: logs, databases, cache
│   ├── log/      ← System logs
│   └── lib/      ← Application state (docker, postgres data)
├── home/         ← Thu muc user
├── root/         ← Thu muc root user
├── tmp/          ← Temporary files (tu dong xoa)
├── usr/          ← User programs, binaries
│   ├── bin/      ← User commands
│   ├── sbin/     ← System admin commands
│   └── local/    ← Locally installed programs
├── opt/          ← Optional/third-party software
├── proc/         ← Process info (virtual filesystem)
├── sys/          ← Kernel info (virtual filesystem)
├── dev/          ← Device files
└── mnt/          ← Mount points
```

## Permissions

```bash
ls -la
# -rwxr-xr-- 1 root www-data 4096 Apr 25 01:00 app.conf
# │├┤├┤├┤
# │ │ │ └── Others: r-- (read only)
# │ │ └──── Group: r-x (read + execute)
# │ └────── Owner: rwx (read + write + execute)
# └──────── File type: - (file), d (directory), l (symlink)
```

### chmod — thay doi permission

```bash
# Bang so
chmod 755 script.sh       # rwxr-xr-x
chmod 644 config.txt      # rw-r--r--
chmod 600 secret.key      # rw-------

# Bang chu
chmod +x script.sh        # Them execute
chmod -w file.txt         # Bo write
chmod u+x,g+r file.txt   # Owner +x, Group +r

# Recursive
chmod -R 755 /var/www/
```

| So | Quyen |
|----|-------|
| 7 | rwx |
| 6 | rw- |
| 5 | r-x |
| 4 | r-- |
| 0 | --- |

### chown — thay doi owner

```bash
chown user:group file.txt
chown -R www-data:www-data /var/www/
chown root file.txt              # Chi doi owner
chown :docker file.txt           # Chi doi group
```

## Links

```bash
# Symbolic link (shortcut)
ln -s /etc/nginx/nginx.conf ~/nginx.conf
ls -la ~/nginx.conf    # → /etc/nginx/nginx.conf

# Hard link
ln /path/to/file /path/to/hardlink
```

## Disk usage

```bash
du -sh /var/log/           # Tong dung luong thu muc
du -sh /var/log/*           # Dung luong tung item
du -sh /var/log/* | sort -h # Sap xep theo size
```
