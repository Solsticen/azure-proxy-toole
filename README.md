# Azure SOCKS5 Proxy Tool - Japan East

Tool tự động tạo SOCKS5 proxy trên Microsoft Azure bằng **Azure Cloud Shell**, sử dụng region **Japan East** (`japaneast`).

## Tính năng

- Đăng ký Resource Provider cần thiết
- Tạo Resource Group
- Tạo VM Ubuntu
- Mở port proxy trong NSG Azure
- Mở port trong firewall của VPS
- Cài và cấu hình `3proxy`
- Xuất proxy ra file `proxies.txt`
- Test proxy sau khi tạo
- Hỗ trợ tạo thêm proxy
- Hỗ trợ fix proxy lỗi
- Hỗ trợ xoá từng proxy hoặc xoá toàn bộ Resource Group

## Cấu hình mặc định

| Tham số | Giá trị |
|---------|---------|
| Resource Group | `azure-proxy-rg` |
| Region | `japaneast` |
| VM Name Prefix | `proxy-jpe` |
| VM Size | `Standard_B2ts_v2` |
| Proxy Username | `japan` |
| Proxy Password | `japn` |
| Proxy Port | `1080` |
| Admin Username VPS | `ubuntu` |
| Image | `Ubuntu2204` |

Proxy sau khi tạo sẽ có dạng: `IP:1080:japan:japn`

## Cách chạy nhanh trong Azure Cloud Shell

1. Vào: https://portal.azure.com/
2. Mở: Cloud Shell → Bash
3. Chạy:

```bash
wget -O azure_proxy_bootstrap.sh https://raw.githubusercontent.com/<your-username>/azure-proxy-tool/main/azure_proxy_bootstrap.sh
chmod +x azure_proxy_bootstrap.sh
bash azure_proxy_bootstrap.sh
