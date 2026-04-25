# Bash Basics

> Ngay bat dau: ___

## Script co ban

```bash
#!/bin/bash
# Dong dau tien bat buoc: shebang

echo "Hello DevOps"

# Chay script
chmod +x script.sh
./script.sh
# hoac: bash script.sh
```

## Variables

```bash
NAME="DevOps"
echo "Hello $NAME"
echo "Hello ${NAME}!"

# Command substitution
TODAY=$(date +%Y-%m-%d)
HOST=$(hostname)
echo "Server: $HOST, Date: $TODAY"

# Read input
read -p "Nhap ten: " USERNAME
echo "Hello $USERNAME"
```

## Conditions

```bash
# If/else
if [ "$STATUS" = "200" ]; then
    echo "OK"
elif [ "$STATUS" = "500" ]; then
    echo "Server Error"
else
    echo "Unknown: $STATUS"
fi

# So sanh so
if [ "$COUNT" -gt 100 ]; then
    echo "Qua nhieu"
fi
# -eq (=), -ne (!=), -gt (>), -lt (<), -ge (>=), -le (<=)

# File checks
if [ -f "/etc/nginx/nginx.conf" ]; then
    echo "File ton tai"
fi
# -f (file), -d (directory), -e (exists), -r (readable), -w (writable)
```

## Loops

```bash
# For
for server in web1 web2 web3; do
    echo "Checking $server"
    ping -c 1 $server
done

# For voi range
for i in $(seq 1 10); do
    echo "Request $i"
done

# While
while true; do
    curl -s http://localhost/health
    sleep 5
done

# Read file line by line
while IFS= read -r line; do
    echo "$line"
done < servers.txt
```

## Functions

```bash
check_health() {
    local url=$1
    local status=$(curl -s -o /dev/null -w "%{http_code}" "$url")
    if [ "$status" = "200" ]; then
        echo "✅ $url: OK"
    else
        echo "❌ $url: $status"
    fi
}

check_health "http://localhost:5001/health"
check_health "http://localhost:80/nginx_status"
```

## Exit codes

```bash
# $? = exit code cua lenh truoc
command
echo $?     # 0 = success, != 0 = error

# Dung trong script
set -e      # Dung ngay khi co loi
set -u      # Loi khi dung bien chua khai bao
set -o pipefail  # Pipe fail neu bat ky lenh nao fail

# Best practice: dat o dau script
set -euo pipefail
```
