# Linux Users & Permissions

> Ngay bat dau: ___

## User management

```bash
# Xem user hien tai
whoami
id                              # UID, GID, groups

# Tao user
useradd -m -s /bin/bash devops  # -m: tao home dir, -s: shell
passwd devops                    # Dat password

# Xoa user
userdel -r devops               # -r: xoa ca home dir

# Doi shell
chsh -s /bin/zsh devops

# Xem tat ca users
cat /etc/passwd
getent passwd
```

## Groups

```bash
# Tao group
groupadd docker

# Them user vao group
usermod -aG docker devops       # -a: append, -G: group

# Xem groups cua user
groups devops
id devops

# Xoa user khoi group
gpasswd -d devops docker
```

## sudo

```bash
# Chay lenh voi quyen root
sudo command
sudo -i                         # Chuyen sang root shell

# Them user vao sudoers
usermod -aG sudo devops         # Ubuntu/Debian
usermod -aG wheel devops        # CentOS/RHEL

# Edit sudoers an toan
visudo
# Them dong: devops ALL=(ALL) NOPASSWD: ALL
```

## SSH

```bash
# Tao SSH key
ssh-keygen -t ed25519 -C "devops@company.com"

# Copy public key den server
ssh-copy-id user@server-ip

# Ket noi
ssh user@server-ip
ssh -p 2222 user@server-ip      # Port khac
ssh -i ~/.ssh/mykey user@server  # Key cu the

# SSH config (~/.ssh/config)
cat << 'EOF' > ~/.ssh/config
Host prod-server
    HostName 10.1.1.100
    User devops
    Port 22
    IdentityFile ~/.ssh/prod_key
EOF

# Sau do chi can:
ssh prod-server
```

## File transfer

```bash
# SCP
scp file.txt user@server:/path/to/dest/
scp -r dir/ user@server:/path/to/dest/
scp user@server:/path/to/file.txt ./

# Rsync (tot hon SCP cho file lon / nhieu file)
rsync -avz ./dir/ user@server:/path/to/dest/
rsync -avz --progress ./dir/ user@server:/path/to/dest/
```
