# 🚀 SSH vào Termux qua Cloudflare Tunnel

Hướng dẫn này giúp bạn mở **SSH server trên Termux**, tạo tunnel bằng **Cloudflare**, và kết nối từ **PC Windows**.

---

## 📱 Cài đặt và cấu hình trên điện thoại (Termux)

1. **Cài đặt OpenSSH**

   ```bash
   pkg update && pkg upgrade -y
   pkg install openssh -y
   ```

2. **Đặt mật khẩu cho user hiện tại**

   ```bash
   passwd
   ```

   👉 Nhớ mật khẩu để đăng nhập sau này.

3. **Khởi động SSH server trên cổng 8022**

   ```bash
   sshd -p 8022
   ```

4. **Cài đặt Cloudflared**

   ```bash
   pkg install cloudflared -y
   ```

5. **Tạo tunnel TCP cho SSH**

   ```bash
   cloudflared tunnel --url tcp://localhost:8022
   ```

   Sau khi chạy, bạn sẽ nhận được một URL dạng:

   ```
   https://example-random-tunnel.trycloudflare.com
   ```

   👉 Copy lại `hostname` này để dùng trên PC.

---

## 💻 Trên máy tính (Windows)

1. **Tải Cloudflared cho Windows**
   Mở PowerShell và chạy:

   ```powershell
   Invoke-WebRequest -Uri https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-windows-amd64.exe -OutFile cloudflared.exe
   ```

2. **Chạy Cloudflared client để kết nối tunnel**
   Trong thư mục chứa `cloudflared.exe`:

   ```powershell
   .\cloudflared.exe access tcp --hostname <hostname_từ_termux> --url localhost:8022
   ```

   Ví dụ:

   ```powershell
   .\cloudflared.exe access tcp --hostname example-random-tunnel.trycloudflare.com --url localhost:8022
   ```

3. **SSH vào Termux qua tunnel**
   Mở PowerShell khác:

   ```powershell
   ssh -p 8022 u0_a529@localhost
   ```

   * `u0_a529` là username trong Termux (xem bằng `whoami` trong Termux).
   * Nhập mật khẩu đã đặt ở bước `passwd`.

---

## 🎯 Kết quả

* Bạn đã kết nối thành công vào **Termux trên Android** từ **PC Windows** qua **Cloudflare Tunnel**.
* Không cần mở port trên router, hoạt động ở mọi nơi có Internet.

---

## ⚡ Tips

* Dùng `whoami` trong Termux để biết username (ví dụ: `u0_a529`).

