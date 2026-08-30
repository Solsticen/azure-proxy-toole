# Azure SOCKS5 Proxy Tool (Multi-Region)

Tool tự động tạo và quản lý **SOCKS5 Proxy (3proxy)** trên Microsoft Azure thông qua **Azure Cloud Shell**.

Hỗ trợ **tự chọn Azure Region trực tiếp khi chạy** (Japan East, Japan West, Singapore, Hong Kong, Korea, US...) hoặc nhập region tùy ý mà không cần sửa code thủ công.

---

## 1. Tính năng nổi bật

* **Chọn Region tương tác:** Tự động hỏi và thiết lập Region (`japaneast`, `japanwest`, `southeastasia`...) và prefix VM tương ứng.
* **Tự động toàn bộ:**
  * Tự kiểm tra và đăng ký các Azure Resource Providers (`Microsoft.Compute`, `Microsoft.Network`, `Microsoft.Storage`).
  * Tạo Resource Group, VM Ubuntu 22.04, Static Public IP, Network Security Group (NSG).
  * Tự động cài đặt & cấu hình dịch vụ `3proxy` với xác thực User/Password.
  * Mở firewall VPS (UFW & iptables) và mở Inbound port trên Azure NSG.
  * Tự động kiểm tra (test live) proxy ngay sau khi tạo và xuất ra file `proxies.txt`.
* **Menu điều khiển tiện lợi (`menu.sh`):** Dễ dàng thêm proxy, test hàng loạt, fix lỗi, debug log, đổi region hoặc xóa tài nguyên.

---

## 2. Cách chạy nhanh trong Azure Cloud Shell

1. Truy cập [Azure Portal](https://portal.azure.com/) và mở **Azure Cloud Shell** (chọn môi trường **Bash**).
2. Dán lệnh sau và nhấn **Enter**:

```bash
wget -O azure_proxy_bootstrap.sh https://raw.githubusercontent.com/Solsticen/azure-proxy-tool/main/azure_proxy_bootstrap.sh && chmod +x azure_proxy_bootstrap.sh && bash azure_proxy_bootstrap.sh
```

*(Hoặc dùng lệnh ngắn gọn)*:

```bash
curl -sSL https://raw.githubusercontent.com/Solsticen/azure-proxy-tool/main/azure_proxy_bootstrap.sh | bash
```

3. Giao diện sẽ hiển thị menu chọn Region và số lượng:

```text
[-] Chọn Azure Region để tạo Proxy:
  1) Japan East      (japaneast    - Tokyo)
  2) Japan West      (japanwest    - Osaka) [Mặc định]
  3) Southeast Asia  (southeastasia- Singapore)
  4) East Asia       (eastasia     - Hong Kong)
  5) Korea Central   (koreacentral - Seoul)
  6) West US 3       (westus3      - Phoenix)
  7) Custom Region   (Nhập mã region tùy chỉnh)

Nhập lựa chọn [1-7, Enter = 2 (Japan West)]: 
[-] Số lượng Proxy cần tạo [Mặc định: 1]: 
[-] Azure VM Size [Mặc định: Standard_B2ts_v2]: 
[-] Proxy Username [Mặc định: japan]: 
[-] Proxy Password [Mặc định: japn]: 
[-] Proxy Port     [Mặc định: 1080]: 
```

---

## 3. Quản lý Proxy bằng Menu tương tác

Sau khi cài đặt, bạn có thể mở menu quản trị bất kỳ lúc nào:

```bash
cd ~/azure-proxy-tool
bash menu.sh
```

**Các chức năng trong Menu:**
1. **Tạo thêm Proxy:** Nhập số lượng cần tạo thêm.
2. **Xem danh sách VM:** Liệt kê các máy ảo, IP và trạng thái.
3. **Xem file `proxies.txt`:** Hiển thị danh sách proxy đã xuất.
4. **Test kết nối toàn bộ Proxy:** Kiểm tra xem từng proxy có hoạt động tốt không.
5. **Sửa lỗi 1 Proxy:** Tự động cài lại & khởi động lại `3proxy` trên VM được chỉ định.
6. **Debug 1 Proxy:** Xem logs cloud-init, trạng thái firewall và cổng mạng.
7. **Xóa 1 Proxy:** Xóa sạch VM, Disk, Card mạng, IP Public và NSG của máy ảo đó.
8. **Đổi Region / Cấu hình:** Chuyển đổi vùng để tạo các proxy tiếp theo sang region khác.
9. **Xóa toàn bộ Resource Group:** Dọn dẹp sạch sẽ tài nguyên khi không còn nhu cầu.

---

## 4. Định dạng Proxy & File xuất ra

Danh sách proxy sau khi tạo được lưu tại:

```bash
~/azure-proxy-tool/proxies.txt
```

Định dạng chuẩn:

```text
IP:PORT:USER:PASS
```

Ví dụ:

```text
20.210.123.45:1080:japan:japn
```

---

## 5. Các lệnh chạy trực tiếp qua Script

Ngoài việc sử dụng `menu.sh`, bạn có thể chạy trực tiếp các script con trong thư mục `~/azure-proxy-tool`:

* **Tạo thêm 2 proxy:**
  ```bash
  cd ~/azure-proxy-tool
  bash 03_create_proxies.sh 2
  ```
* **Test lại toàn bộ proxy:**
  ```bash
  bash 04_test_proxies.sh
  ```
* **Sửa lỗi 1 máy ảo (ví dụ `proxy-jpe-001` hoặc `proxy-jpw-001`):**
  ```bash
  bash 05_fix_one_proxy.sh proxy-jpe-001
  ```
* **Xem debug log của 1 máy ảo:**
  ```bash
  bash 08_debug_one_proxy.sh proxy-jpe-001
  ```
* **Xóa 1 máy ảo:**
  ```bash
  bash 06_delete_one_proxy.sh proxy-jpe-001
  ```
* **Xóa toàn bộ Resource Group:**
  ```bash
  bash 07_delete_all_proxy_rg.sh
  ```

---

## 6. Cập nhật lại Tool khi có bản mới

Khi có bản cập nhật mới trên GitHub, trong Cloud Shell bạn chỉ cần chạy:

```bash
rm -rf ~/azure-proxy-tool
wget -O azure_proxy_bootstrap.sh https://raw.githubusercontent.com/Solsticen/azure-proxy-tool/main/azure_proxy_bootstrap.sh && chmod +x azure_proxy_bootstrap.sh && bash azure_proxy_bootstrap.sh
```

*(Lưu ý: Lệnh này chỉ cập nhật thư mục mã nguồn tool trong Cloud Shell, các máy ảo proxy đã tạo trên tài khoản Azure vẫn hoạt động bình thường).*

---

## 7. Khắc phục sự cố thường gặp

* **Lỗi VM Size không khả dụng tại Region đã chọn:**
  * Một số region có thể tạm hết quota cho size `Standard_B2ts_v2`. Bạn có thể đổi sang `Standard_B1s`, `Standard_B1ms` hoặc đổi sang Region khác (ví dụ: `japaneast` $\leftrightarrow$ `japanwest`).
* **Proxy tạo xong nhưng test ban đầu báo fail:**
  * Quá trình cloud-init cài đặt `3proxy` trên máy ảo mất khoảng 30–60 giây sau khi VM khởi động. Script sẽ tự động thử lại 20 lần.
  * Nếu cần cài lại ngay lập tức, dùng lệnh: `bash 05_fix_one_proxy.sh <TÊN_VM>`.
