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
Phần này đơn giản là hướng dẫn người dùng truy cập vào địa chỉ DFS (vd: `\\corp.apple.com\dfs`) để tải dữ liệu.

### Upload (gửi) dữ liệu lên DFS.
Sơ lược hệ thống tại CN như sau:
- Admin CN dùng một máy chủ (Windows Server) có chức năng chia sẻ dữ liệu (File Server) trong nội bộ. Vd: `\\File-Server-CN\dfs`.
- Bên trong thư mục `dfs` này (để phân biệt, từ giờ sẽ gọi là `DFS_CN`) đã được Admin CN sao chép **cấu trúc thư mục** y hệt từ DFS chính (gọi là `DFS_Main`).
- Người dùng cuối (không phải Admin CN) sử dụng tài khoản Active Directory của mình gửi dữ liệu vào các thư mục tương ứng trong `DFS_CN`.
- Trên máy chủ File Server CN có phần mềm đồng bộ tự động định kỳ đồng bộ (1 chiều) từ `DFS_CN` lên `DFS_Main`.

## Cài đặt và Cấu hình

##1. Cấu hình máy chủ File Server CN:
- Windows Server 2008 Standard R2 - 64bit đã join domain `corp.apple.com`. Nên cài và cấu hình thêm `FSRM` (đã có note ở trên).
- Tạo thư mục `dfs_cn` trên một phân vùng bất kỳ. Share thư mục này theo quy định của Apple đảm bảo phân quyền tối thiểu.

##2. Sao chép (clone) cấu trúc thư mục `DFS_Main` về `DFS_CN`:
Xin lưu ý là chỉ clone **cấu trúc thư mục** không bao gồm tập tin.

Có nhiều cách để clone, ở đây chúng ta dùng tiện ích có sẵn của Windows Server là `robocopy`. Đây là một tiện ích khá hay dùng trong việc backup dữ liệu mà các SysAdmin nên biết qua (Google).

- Đăng nhập vào File Server với tài khoản có quyền trên `DFS_Main`. Vd: `california-admin-01@apple.com`. Truy cập vào `corp.apple.com\dfs`.
- Mở `PowerShell` với quyền Administrator.
- Gõ dòng lệnh sau:

```bash
robocopy \\corp.apple.com\dfs e:\dfs_cn /zb /e /xf * /xd DfsrPrivate
```
Ở dòng lệnh trên, các bạn chú ý đoạn `/xd DfsrPrivate`. Đoạn này có ý nghĩa báo cho `robocopy` loại trừ (exclude) thư mục `DfsrPrivate` ra khỏi quá trình clone. Vì Admin CN không có permission trên thư mục này nên nếu "cố đấm ăn xôi" thì kết quả chỉ là ăn hành thôi 😂

#### Note:
- Định kỳ, Admin CN nên thực hiện lại lệnh này để đảm bảo cấu trúc thư mục luôn được cập nhật đầy đủ tránh trường hợp người dùng khi vào `DFS_CN` không tìm ra thư mục để gửi dữ liệu vào.
- Đồng thời nếu thấy báo lỗi `Access denied` trên thư mục nào đó ở `DFS_Main` thì thêm vào `/xd Ten_Thu_Muc_1 /xd Ten_Thu_Muc_2` để exclude ra khỏi quá trình clone.

##3. Cài đặt phần mềm hỗ trợ đồng bộ tự động (auto sync)

- Trên File Server cài đặt phần mềm **FreeFileSync** [https://freefilesync.org/download.php](https://freefilesync.org/download.php) (thấy tên nên mạnh dạn đoán phần mềm miễn phí, anh em tạm khỏi lo khoản "lai-sần")

- Cấu hình **đồng bộ 1 chiều*** từ thư mục `dfs_cn` lên `corp.apple.com\dfs`.

- Lưu thiết lập cấu hình thành tập tin định dạng riêng của FreeFileSync vào đâu đó vd: `D:\Contribute2DFS.ffs_batch`.

###4. Hướng dẫn cấu hình FreeFileSync với Schedule của Windows
Xem [hướng dẫn cấu hình FreeFileSync với Schedule của Windows](https://freefilesync.org/manual.php?topic=schedule-batch-jobs). 

###5. Chạy test, tận hưởng kết quả và ... đi nhậu thôi.