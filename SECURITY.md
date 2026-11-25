# Tình trạng bảo mật

## Việc cần làm ngay

1. Bật Email/Password trong Firebase Console → Authentication → Sign-in method, rồi tạo tài khoản quản trị mới. Trong Authentication → Users, sao chép UID của tài khoản đó.
2. Thay `REPLACE_WITH_ADMIN_UID` trong `database.rules.json` bằng UID vừa sao chép, rồi áp dụng rules trong Realtime Database → Rules (hoặc deploy bằng Firebase CLI từ thư mục này). Trang công khai đọc được `lottery_results`; chỉ UID này mới ghi được kết quả. Quyền mặc định từ chối mọi nhánh khác.
3. Nếu database từng có dữ liệu trong `system_users`, `access_logs` hoặc `system_logs`, xóa các nhánh này trong Firebase Console. Mật khẩu cũ được lưu dưới dạng Base64 hai lớp, không phải hash bảo mật; coi như đã bị lộ và không tái sử dụng.

Trang `admingioan.html` dùng Firebase Authentication email/mật khẩu. Đăng nhập không đủ để cấp quyền ghi: UID tài khoản phải khớp UID trong rule. Đừng thay placeholder bằng quyền ghi cho mọi tài khoản đã đăng nhập. Cấu hình cơ cấu dùng `prize_names`, `prize_counts` và `prize_order`; các nhánh này được đọc công khai, chỉ UID admin được sửa. `admin_meta` lưu thời gian cập nhật gần nhất và chỉ UID admin được đọc/ghi.

## Giới hạn

GitHub Pages chỉ phục vụ tệp tĩnh. Mọi URL Firebase trong JavaScript đều công khai; Base64 không che giấu bí mật. Không đưa service account, Admin SDK key hoặc private key vào repository hay frontend. Firebase Security Rules mới là lớp bắt buộc để kiểm soát quyền truy cập.

Trang công khai không thu thập IP, vị trí gần đúng, user-agent hay thông tin thiết bị. Bộ đếm lượt xem được khôi phục qua transaction; rules chỉ cho phép tăng tuần tự từng lượt, nhưng client vẫn có thể spam transaction. Nếu cần bộ đếm chống giả mạo, chuyển phần ghi sang backend. Trang quản trị hiện dùng Firebase Authentication email/mật khẩu và UID được giới hạn trong Database Rules.

Danh sách vé bán (`ticket_sales`) gồm tên và liên hệ người mua; trạng thái phát quà (`prize_claims`) và lịch sử tạo/chuyển vé (`ticket_history`, `ticket_transfers`) cũng là dữ liệu riêng. Chỉ UID quản trị được đọc/ghi các nhánh này theo Firebase Rules. Không thêm quyền đọc công khai cho các nhánh này.
