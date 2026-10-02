# Báo Cáo Bài Tập 1: Khởi Tạo Máy Chủ Cloud / Droplet & Cấu Hình Xác Thực SSH Key

- **Môn học:** IT209
- **Buổi học:** Session 02 - Cloud Infrastructure & DevOps Basics
- **Bài tập:** Bài 1 - Khởi tạo Cloud Server / Droplet & Cấu hình SSH Key
- **Học viên:** Hoang Thien Son (hoangthienson06@gmail.com)

---

## 1. Mục tiêu bài thực hành
- Thực hành đăng ký tài khoản, lựa chọn cấu hình phần cứng phù hợp và khởi tạo thành công máy chủ ảo chạy hệ điều hành **Ubuntu 22.04 LTS** trên hạ tầng Cloud / VPS.
- Cấu hình xác thực an toàn bằng cặp khóa SSH (**SSH Keypair**) chuẩn `ed25519` thay cho mật khẩu thô.
- Thực hiện kết nối thành công tới máy chủ thông qua SSH từ máy tính cá nhân bằng tài khoản `root` mà không cần nhập mật khẩu.

---

## 2. Thông số cấu hình máy chủ

| Thuộc tính | Giá trị cấu hình | Ghi chú |
| :--- | :--- | :--- |
| **Nhà cung cấp hạ tầng** | AZVPS (Hạ tầng Cloud / KVM VPS) | Tương đương DigitalOcean Basic Droplet |
| **Hệ điều hành (OS)** | **Ubuntu 22.04.1 LTS** (x64) | Bản LTS ổn định, chuẩn DevOps |
| **Hostname** | `azvps-tm8ltzu1c` | Tên định danh máy chủ |
| **Địa chỉ IPv4 Public** | **`165.101.46.48`** | IP công khai để truy cập SSH |
| **Cấu hình phần cứng** | 2.048 MB RAM (2 GB) / 20 GB SSD | Cấu hình tối ưu tiết kiệm chi phí |
| **Phương thức xác thực** | **SSH Key (`id_ed25519`)** | Không dùng mật khẩu thô |
| **Tài khoản truy cập** | `root` | Tài khoản quản trị cao nhất |

---

## 3. Các bước triển khai chi tiết

### Bước 1: Tạo cặp khóa SSH (SSH Keypair) trên máy tính cá nhân
Sử dụng thuật toán hiện đại và an toàn nhất hiện nay là `ed25519`:
```bash
ssh-keygen -t ed25519 -C "hoangthienson06@gmail.com" -f ~/.ssh/id_ed25519 -N ""
```

Xem khóa công khai (Public Key):
```bash
cat ~/.ssh/id_ed25519.pub
```
> **Khóa công khai (Public Key):**
> ```text
> ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIGrKTlnSP8s+3giWM3yqzud26R3GGQhho0K3Eq1Gzhq2 hoangthienson06@gmail.com
> ```

---

### Bước 2: Khởi tạo máy chủ Ubuntu 22.04 LTS
- Truy cập bảng điều khiển quản trị máy chủ (Console).
- Lựa chọn hệ điều hành **Ubuntu 22.04 LTS (Jammy Jellyfish)**.
- Gói cấu hình: 2GB RAM / 20GB SSD / IPv4 Public: `165.101.46.48`.

---

### Bước 3: Đẩy Public Key lên máy chủ để xác thực an toàn
Cài đặt khóa công khai `id_ed25519.pub` vào danh sách khóa được ủy quyền (`~/.ssh/authorized_keys`) trên máy chủ:
```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub root@165.101.46.48
```
> **Kết quả:** `Number of key(s) added: 1`. Khóa SSH đã được nạp thành công vào máy chủ.

---

### Bước 4: Kết nối và kiểm tra từ Terminal máy cá nhân
Chạy lệnh kết nối sử dụng Private Key:
```bash
ssh -i ~/.ssh/id_ed25519 root@165.101.46.48
```

---

## 4. Kết quả kiểm tra thực tế & Terminal Logs

### 4.1. Lệnh kiểm tra kết nối:
```bash
ssh -i ~/.ssh/id_ed25519 root@165.101.46.48
```

### 4.2. Nhật ký (Log) hiển thị khi đăng nhập thành công:
```text
Welcome to Ubuntu 22.04.1 LTS (GNU/Linux 5.15.0-46-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Fri Oct  2 07:03:51 AM +07 2026

  System load:  0.16015625         Processes:             89
  Usage of /:   20.8% of 19.59GB   Users logged in:       0
  Memory usage: 12%                IPv4 address for eth0: 165.101.46.48
  Swap usage:   0%

356 updates can be applied immediately.
252 of these updates are standard security updates.
```

### 4.3. Kiểm tra thông tin hệ thống trên máy chủ:
```bash
root@azvps-tm8ltzu1c:~# uname -a
Linux azvps-tm8ltzu1c 5.15.0-46-generic #49-Ubuntu SMP Thu Aug 4 18:03:25 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux

root@azvps-tm8ltzu1c:~# lsb_release -a
Distributor ID: Ubuntu
Description:    Ubuntu 22.04.1 LTS
Release:        22.04
Codename:       jammy

root@azvps-tm8ltzu1c:~# whoami
root

root@azvps-tm8ltzu1c:~# free -m
               total        used        free      shared  buff/cache   available
Mem:            1975         187         717           2        1070        1621
Swap:              0           0           0

root@azvps-tm8ltzu1c:~# df -h /
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda1        20G  4.1G   15G  22% /
```

---

## 5. Đánh giá & Kết luận
1. **Xác thực SSH Key**: Kết nối thành công tới máy chủ thông qua khóa `~/.ssh/id_ed25519` mà không cần nhập mật khẩu, đáp ứng đúng yêu cầu bảo mật.
2. **Hệ điều hành & Tài nguyên**: Máy chủ vận hành trơn tru trên **Ubuntu 22.04.1 LTS**, dung lượng đĩa 20GB và bộ nhớ RAM 2GB sẵn sàng phục vụ cho các bài tập triển khai DevOps tiếp theo.
