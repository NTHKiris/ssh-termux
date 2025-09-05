# 🚀 SSH & MariaDB qua Cloudflare Tunnel

Hướng dẫn này giúp bạn mở **SSH server** và **MariaDB server** trên Termux, tạo tunnel bằng **Cloudflare**, và kết nối từ **PC Windows** (bao gồm cả **DBeaver**).

---

## 📱 Cài đặt và cấu hình trên điện thoại (Termux)

### 1. SSH

1. **Cài đặt OpenSSH**

   ```bash
   pkg update && pkg upgrade -y
   pkg install openssh -y
   ```

2. **Đặt mật khẩu cho user hiện tại**

   ```bash
   passwd
   ```

3. **Khởi động SSH server trên cổng 8022**

   ```bash
   sshd -p 8022
   ```

### 2. MariaDB

1. **Cài đặt MariaDB**

   ```bash
   pkg install mariadb -y
   ```

2. **Khởi động MariaDB**

   ```bash
   mysqld_safe &
   ```

3. **Truy cập MariaDB**

   ```bash
   mysql -u root -p
   ```

4. **Tạo user để kết nối từ DBeaver**

   ```sql
   CREATE USER 'dbeaver'@'%' IDENTIFIED BY 'password123';
   GRANT ALL PRIVILEGES ON *.* TO 'dbeaver'@'%' WITH GRANT OPTION;
   FLUSH PRIVILEGES;
   ```

### 3. Cài đặt Cloudflared

```bash
pkg install cloudflared -y
```

#### Tạo tunnel cho SSH

```bash
cloudflared tunnel --url tcp://localhost:8022
```

#### Tạo tunnel cho MariaDB

```bash
cloudflared tunnel --url tcp://localhost:3306
```

👉 Mỗi lệnh sẽ trả về một **hostname** dạng:

```
https://example-random-tunnel.trycloudflare.com
```

Copy lại để dùng trên PC.

---

## 💻 Trên máy tính (Windows)

### 1. Tải Cloudflared cho Windows

```powershell
Invoke-WebRequest -Uri https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-windows-amd64.exe -OutFile cloudflared.exe
```

### 2. Chạy Cloudflared client

#### Cho SSH

```powershell
.\cloudflared.exe access tcp --hostname <hostname_từ_termux> --url localhost:8022
```

#### Cho MariaDB

```powershell
.\cloudflared.exe access tcp --hostname <hostname_từ_termux> --url localhost:3306
```

### 3. SSH vào Termux

```powershell
ssh -p 8022 u0_a529@localhost
```

* `u0_a529` là username trong Termux (xem bằng `whoami`).
* Nhập mật khẩu đã đặt bằng `passwd`.

### 4. Kết nối DBeaver tới MariaDB

* **Database type:** MariaDB
* **Host:** `localhost`
* **Port:** `3306`
* **Username:** `dbeaver` (hoặc user bạn tạo)
* **Password:** `password123`

👉 Nhấn **Test Connection** → nếu thấy OK thì Save lại.

---

## 🎯 Kết quả

* Kết nối thành công **SSH từ Windows → Termux** qua Cloudflare Tunnel.
* Quản lý **MariaDB trên Termux** bằng **DBeaver** trên Windows.
* Hoạt động ở mọi nơi có Internet, không cần mở port router.

---

## ⚡ Tips

* Dùng `whoami` trong Termux để biết username (ví dụ: `u0_a529`).
* Luôn chạy lại `cloudflared tunnel` mỗi khi khởi động mới Termux.
* Có thể viết script `.sh` (Termux) và `.bat` (Windows) để tự động hóa.
