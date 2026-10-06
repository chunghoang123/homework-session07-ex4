# Bài 4: Reverse Proxy Nginx cho Spring Boot

## 1. Trang tĩnh `/var/www/html/index.html`
Tạo file `index.html` (xem file trong repo) hiển thị Họ tên + Mã lớp, copy lên server:
```bash
sudo cp index.html /var/www/html/index.html
```

## 2. Virtual host `/etc/nginx/sites-available/spring-proxy.conf`
Nội dung đầy đủ xem file `spring-proxy.conf` trong repo này:
- `listen 80`, `root /var/www/html`
- `location /` phục vụ file tĩnh
- `location /api/` proxy về backend kèm header chuyển tiếp:
```nginx
proxy_pass http://127.0.0.1:8082;
proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto $scheme;
```
Kích hoạt:
```bash
sudo cp spring-proxy.conf /etc/nginx/sites-available/spring-proxy.conf
sudo ln -sf /etc/nginx/sites-available/spring-proxy.conf /etc/nginx/sites-enabled/
sudo rm -f /etc/nginx/sites-enabled/default
sudo nginx -t
sudo systemctl reload nginx
```

## 3. Kiểm tra
```
$ sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful

$ curl -I http://localhost/
HTTP/1.1 200 OK
Server: nginx/1.24.0 (Ubuntu)
Content-Type: text/html
-> trang / tra ve 200 kem noi dung tinh chua Ho ten + Ma lop

$ curl http://localhost/api/health
{"status":"UP"} (HTTP 200)
-> khong 502, backend Spring Boot (Ex3) tra loi qua proxy
```
