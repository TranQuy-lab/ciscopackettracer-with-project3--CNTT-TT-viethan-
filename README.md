# Đồ án Chuyển mạch và Định tuyến - Hạ tầng Mạng Campus VKU
Repository này lưu trữ tài liệu, file cấu hình Cisco Packet Tracer và báo cáo kỹ thuật cho hệ thống mạng mô phỏng của Đại học Công nghệ Thông tin và Truyền thông Việt - Hàn (VKU). 

## 1. Thông tin Đồ án
* **Sinh viên thực hiện:** Trần Cao Trọng Quý (Lớp 24NS - Chuyên ngành An toàn Thông tin)
* **Giảng viên hướng dẫn:** TS. Hoàng Hữu Đức & TS. Nguyễn Nho Túy
* **Mục tiêu:** Thiết kế, tối ưu định tuyến và áp dụng các chính sách bảo mật chặt chẽ cho hạ tầng kết nối giữa cơ sở chính (Khu K) và chi nhánh (Khu V).

## 2. Tổng quan Kiến trúc Hệ thống
Mô hình được xây dựng và kiểm thử trên Cisco Packet Tracer 8.2+, bao gồm các phân khu logic:
* **Core Layer (Khu K):** 2x Switch Multilayer 3560 (`CORE-SW1`, `CORE-SW2`) chạy dự phòng HSRP và gom băng thông bằng LACP EtherChannel. Định tuyến trực tiếp mạng nội bộ và Server Farm (VLAN 200).
* **Security & DMZ:** Tường lửa Cisco ASA 5506-X phân tách 3 vùng an ninh (Inside, Outside, DMZ) thông qua các cổng Routed GigabitEthernet. Cấu hình ACL và Inspect traffic để kiểm soát luồng dữ liệu.
* **Edge & WAN:** Router ISR 4331 (`EDGE-K`) đảm nhận NAT ra Internet và thiết lập đường hầm VPN IPsec Site-to-Site bảo mật tới `ROUTER-V` (Khu V).
* **Access Layer:** Hệ thống Switch 2960 phân bổ cho các tòa (A, B, C, Hành chính, ESTI, LAB) và tích hợp Controller WLC để quản lý các Access Point tập trung.

## 3. Các Công nghệ & Cơ chế Bảo mật Áp dụng
Là một hệ thống chú trọng vào khía cạnh an toàn thông tin, mạng được tích hợp nhiều lớp bảo vệ:
* **Chuyển mạch & Chống Loop:** Sử dụng Rapid PVST+ và VTP Transparent mode để ngăn chặn lỗi đồng bộ xóa cấu hình VLAN.
* **Bảo mật Lớp 2 (Access Layer):** * Áp dụng Port Security (Sticky MAC, Maximum) để chống cắm thiết bị trái phép.
  * Triển khai DHCP Snooping và Dynamic ARP Inspection để chống giả mạo DHCP server và tấn công ARP Spoofing.
* **Bảo mật Lớp 3 & VPN:** * Phân quyền truy cập bằng ACL trên Switch Core (giới hạn truy cập vào LAB/Sinh viên) và Firewall ASA.
  * Toàn bộ dữ liệu giao tiếp giữa Khu K và Khu V được mã hóa thông qua IPsec VPN (AES & SHA).
* **QoS & Dịch vụ:** NAT Overload ra Internet, Static NAT cho Web Server DMZ, và áp dụng CBWFQ để ưu tiên băng thông cho mạng WiFi.

## 4. Hướng dẫn Kiểm tra (Testing & Verification)
Để kiểm tra tính thông suốt và các chính sách bảo mật, hãy tải file `.pkt` và chạy các lệnh xác minh sau:

### 4.1. Kiểm tra Tính sẵn sàng cao (High Availability)
Thực hiện trên `CORE-SW1` hoặc `CORE-SW2`:
```bash
show standby brief       # Kiểm tra trạng thái Active/Standby của gateway HSRP
show etherchannel summary # Kiểm tra gom link Port-Channel 1
