# Hướng dẫn cài đặt, cấu hình DFS mirror ở File Server của CN

## Mô tả
Do cơ chế phân quyền DFS của TT.CNTT hiện tại chưa mở rộng nên IT Admin CN là người phải gửi báo cáo. Việc triển khai hệ thống ở tài liệu này sẽ giúp IT tự động hóa việc gửi dữ liệu báo cáo.

## Hạ tầng
- Máy chủ `Windows Server 2008 Standard` 64 bit.
- Máy chủ trên đóng vai trò File Server tại CN.
- Máy chủ nên cài `File Server Resource Manager` để chủ động được `quota` và file `mines-types`. (hỏi thêm Google)

## Cài đặt và Cấu hình

