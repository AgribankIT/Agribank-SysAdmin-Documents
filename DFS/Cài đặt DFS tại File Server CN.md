# Hướng dẫn cài đặt, cấu hình DFS mirror ở File Server của CN

## Mô tả
Do cơ chế phân quyền DFS của TT.CNTT hiện tại chưa mở rộng nên IT Admin CN là người phải gửi báo cáo. Việc triển khai hệ thống ở tài liệu này sẽ giúp IT tự động hóa việc gửi dữ liệu báo cáo.

## Hạ tầng
- Máy chủ `Windows Server 2008 Standard` 64 bit.
- Máy chủ trên đóng vai trò File Server tại CN.
- Máy chủ nên cài `File Server Resource Manager` để chủ động được `quota` và file `mines-types`. (hỏi thêm Google)


## Tổng quan hệ thống

Tài liệu này tập trung hướng đến cấu hình tại CN cho việc gửi báo cáo tự động không cần sự can thiệp quá nhiều của quản trị hệ thống (Admin).

### Download dữ liệu từ DFS
Phần này đơn giản là hướng dẫn người dùng truy cập vào địa chỉ DFS (vd: `\\data.apple.com\dfs`) để tải dữ liệu.

### Upload (gửi) dữ liệu lên DFS.
Sơ lược hệ thống tại CN như sau:
- Admin CN dùng một máy chủ (Windows Server) có chức năng chia sẻ dữ liệu (File Server) trong nội bộ. Vd: `\\File-Server-CN\dfs`.
- Bên trong thư mục `dfs` này (để phân biệt, từ giờ sẽ gọi là `DFS_CN`) đã được Admin CN sao chép **cấu trúc thư mục** y hệt từ DFS chính (gọi là `DFS_Main`).
- Người dùng cuối (không phải Admin CN) sử dụng tài khoản Active Directory của mình gửi dữ liệu vào các thư mục tương ứng trong `DFS_CN`.
- Trên máy chủ File Server CN có phần mềm đồng bộ tự động định kỳ đồng bộ (1 chiều) từ `DFS_CN` lên `DFS_Main`.

## Cài đặt và Cấu hình

