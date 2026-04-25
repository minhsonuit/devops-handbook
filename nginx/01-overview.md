# Nginx Overview

## Nginx thuong dong vai tro gi

- Reverse proxy
- TLS termination
- Load balancer
- Static file server
- Gateway vao he thong noi bo

## Luong request pho bien

```text
Client -> Firewall / Load Balancer -> Nginx -> App / UI / API / Notify
```

Trong mo hinh cua ban, cach nghi dung la:

- Tu ngoai vao Nginx: thuong la HTTPS
- Tu Nginx vao service noi bo: co the la HTTP hoac HTTPS tuy kien truc
- Nginx la diem tap trung de route, log, chan IP va quan sat

## Nhung khoi config quan trong

- `main`: cau hinh toan cuc
- `events`: worker, connection
- `http`: cau hinh HTTP chung
- `upstream`: nhom backend
- `server`: virtual host theo host/port
- `location`: route theo path

## Nguyen tac thuc dung

- Tach Nginx thanh edge ro rang: TLS, whitelist, route
- Ghi log du gia tri de debug
- Validate truoc reload
- Khong mo endpoint quan sat nhu `nginx_status` ra public neu khong can
- Han che hard-code IP neu da co `upstream` hoac DNS noi bo
