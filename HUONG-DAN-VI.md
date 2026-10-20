# Hướng dẫn triển khai School Awards (miễn phí)

## Website sẽ làm được gì?
- Công khai một đường link cho cả lớp.
- Hiển thị 25 thí sinh và 5 danh hiệu: Miss Beauty, Miss Connection, Miss Friendship, Miss Humour, School Princess.
- Không yêu cầu người bình chọn đăng nhập.
- Lưu phiếu bầu và ảnh tập trung để mọi người xem cùng kết quả.
- Chỉ tài khoản Host đã được cấp quyền mới đăng nhập để tải ảnh hoặc tạm dừng/mở lại bình chọn.

## Bạn cần tự thực hiện
Bạn cần tự tạo tài khoản Supabase và GitHub, tạo dự án, dán SQL, tạo tài khoản Host, điền URL/anon key và bật GitHub Pages. ChatGPT không thể tự đăng ký tài khoản hoặc chấp nhận điều khoản thay bạn.

## A. Tạo cơ sở dữ liệu và nơi lưu ảnh trên Supabase
1. Mở https://supabase.com/ và đăng ký/đăng nhập.
2. Tạo một project mới. Lưu mật khẩu database ở nơi an toàn; không đưa mật khẩu đó vào website.
3. Trong project, mở **SQL Editor** → **New query**.
4. Mở tệp `supabase.sql` trong gói ZIP, sao chép toàn bộ nội dung, dán vào SQL Editor và nhấn **Run**.
5. Mở **Authentication → Users**. Tạo user cho riêng bạn bằng email và mật khẩu. Nếu Supabase yêu cầu xác nhận email, hãy xác nhận.
6. Tại danh sách Users, sao chép UUID của tài khoản Host vừa tạo.
7. Quay lại SQL Editor, chạy câu lệnh sau và thay phần placeholder bằng UUID thật:
   ```sql
   insert into public.admins(user_id)
   values ('DAN-UUID-TAI-KHOAN-HOST-VAO-DAY');
   ```
   Chỉ thêm UUID tài khoản của bạn. Đây là bước cấp quyền Host.
8. Mở **Project Settings → API** (hoặc mục Connect/API keys tuỳ giao diện). Sao chép **Project URL** và **anon/public key**. Không bao giờ dùng `service_role` hoặc secret key trong website.
9. Mở tệp `config.js` và thay hai giá trị:
   ```js
   window.SUPABASE_CONFIG = {
     url: "https://YOUR_PROJECT_ID.supabase.co",
     anonKey: "YOUR_SUPABASE_ANON_PUBLIC_KEY"
   };
   ```
   bằng Project URL và anon/public key của bạn. Lưu tệp.

## B. Đăng website miễn phí bằng GitHub Pages
1. Mở https://github.com/ và tạo tài khoản/đăng nhập.
2. Tạo repository mới, ví dụ `school-awards`. Vì website có thể chứa ảnh của học sinh, cân nhắc kỹ trước khi để repository công khai; không đưa ảnh thật hoặc thông tin cá nhân vào mã nguồn.
3. Giải nén `school-awards-online-package.zip` trên máy tính.
4. Tải `index.html` và `config.js` đã điền cấu hình lên thư mục gốc của repository. Có thể tải `supabase.sql` và `README.md` lên để lưu tài liệu, nhưng không bắt buộc cho website chạy.
5. Vào **Settings → Pages**. Trong phần build/deployment, chọn **Deploy from a branch**, chọn nhánh `main` và thư mục `/ (root)`, sau đó Save.
6. Chờ GitHub Pages triển khai. Đường link thường có dạng `https://TEN-TAI-KHOAN.github.io/school-awards/`; dùng URL GitHub hiển thị trong Settings → Pages làm URL chính thức.
7. Trong Supabase, vào **Authentication → URL Configuration** và cập nhật Site URL bằng đường link GitHub Pages. Nếu cần, thêm URL đó vào Redirect URLs.
8. Mở website công khai. Kéo xuống **Host / Admin**, nhập email và mật khẩu Supabase của bạn để đăng nhập. Chọn ảnh từng thí sinh; ảnh sẽ được lưu trên Supabase và hiển thị cho người khác.
9. Mở website trong cửa sổ ẩn danh hoặc trên điện thoại khác để kiểm tra: ảnh, phiếu bầu và kết quả có đồng bộ không.

## C. Quy tắc bình chọn và giới hạn
- Người dùng không cần đăng nhập để bỏ phiếu.
- Vì không yêu cầu đăng nhập hoặc xác minh danh tính, một người có thể gửi nhiều phiếu. Không thể bảo đảm “mỗi người một phiếu” trong chế độ bình chọn tự do này.
- Host có thể tạm dừng/mở lại bình chọn trong mục Host controls.
- Kết quả được hiển thị theo tổng số phiếu mỗi thí sinh, mỗi danh hiệu.
- Không đăng ảnh nếu chưa có quyền sử dụng ảnh của người trong ảnh, đặc biệt nếu họ là học sinh vị thành niên. Chỉ chia sẻ đường link với đối tượng phù hợp.

## D. Nếu gặp lỗi
- **Setup required:** kiểm tra đã điền đúng URL và anon/public key trong `config.js` chưa.
- **Connection error / dữ liệu không tải:** chạy lại `supabase.sql` trong SQL Editor và kiểm tra project có đang hoạt động không.
- **Host sign-in failed:** kiểm tra email/mật khẩu, xác nhận email nếu được yêu cầu; đảm bảo UUID tài khoản đã được thêm vào bảng `public.admins`.
- **Upload bị từ chối:** kiểm tra tài khoản có trong `public.admins` và SQL policies đã được chạy.
- **GitHub Pages chưa cập nhật:** chờ deployment hoàn tất, tải lại trang cứng (hard refresh), kiểm tra `config.js` đã được commit và publish.

## An toàn
`anon/public key` có thể xuất hiện ở website; chính sách Row Level Security trong SQL giới hạn thao tác. Tuyệt đối không công khai database password hoặc `service_role`/secret key. Vì bình chọn tự do, website phù hợp cho một cuộc bình chọn vui vẻ, không phải bầu cử có yêu cầu chống gian lận.
