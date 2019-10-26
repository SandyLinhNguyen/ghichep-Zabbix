# ghichep-Zabbix

## 1. Zabbix là gì ?

**Zabbix** là một dịch vụ mã nguồn mở phục vụ cho việc monitor các hệ thống máy chủ, mạng với nhiều tính năng độc đáo và khả năng tuy biến cao. 

Zabbix được sáng lập bởi Alexei Vladishezv và hiện nay đang được phát triển và hỗ trợ bởi tổ chứ Zabbix SIA.

- Ưu điểm:
  - Hỗ trợ máy Agent trên nhiều hệ điều hành (Linux, Windown, Mac OS)
  - Giao diện đẹp mắt.
  - Mã nguồn mở, đầu tư ít.
  - Có nhiều plugin hỗ trợ nhiều dịch vụ khác nhau.
- Nhược điểm:
  - Không hỗ trợ GUI on Mobile
  - Việc cài đặt Alert còn phức tạp 

## 2. Mô hình hoạt động của Zabbix 

- Zabbix có thể thu thập metric dựa vào zabbix-agent, smnp hoặc IPMI sau đó đẩy về Zabbix server để phân tích.
- Zabbix server dựa vào metric nhận được để tính toán, vẽ biểu đồ và cảnh báo cho người dùng dựa vào các trigger được định nghĩa.
- Sau khi metric được zabbix server phân tích xong sẽ được đẩy vào lưu trữ trong Database.
- Zabbix Web dùng để hiển thị các metric thông qua biểu đồ để theo dõi.


## 3. Hướng dẫn cài đặt Zabbix

[Cài đặt Zabbix](./docs/install-zabbix.md)

