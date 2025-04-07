# 1. Broadcasting trong Laravel
Broadcasting (phát sóng sự kiện) trong Laravel là cơ chế cho phép Laravel gửi (phát) các sự kiện từ servẻ ra ngoài frontend (JS) qua WebSockets hoặc các dịch vụ như Pusher, Ably, Redis, ...
- Nó là cầu nối giữa Laravel và client, để các event không chỉ xảy ra trên backend mà còn được gửi ra ngoài fontend (real-time).

# 2. Pusher
- Pusher là một dịch vụ **gửi dữ liệu thời gian thực** từ server đến client
- Nó hoạt động dựa trên WebSockets, giúp "đẩy" **dữ liệu từ server xuống client ngay khi có sự thay đổi**, mà không cần client phải "hỏi" server liên tục.

➡️ Tưởng tượng Pusher giống như một anh bưu tá siêu nhanh: cứ có tin nhắn mới (event), là ảnh phi ngay đến người nhận để thông báo liền


# 3. Laravel Echo
- Laravel Echo là thư viện Javascript được laravel phát triển để nghe các event mà laravel phát ra (thường qua Pusher hoặc các dịch vụ tương tự).

➡️ Tưởng tượng Laravel Echo như chiếc điện thoại thông minh của người dùng: nó bật sẵn, và khi có "cuộc gọi" (event từ server), nó sẽ ngay lập tực nhận cuộc gọi đó và hiển thị thông báo ngay cho nguời dùng.


# 4. Mối quan hệ giữa Laravel, Pusher và Echo
- Laravel phát một sự kiện (event) -> gửi qua Pusher
- Pusher truyền sự kiện đó tới client đang mở ứng dụng
- Laravel Echo (ở client) đang "lắng nghe", và nhận được sự kiện ngay lập tức.

# 5. Flow và các thành phần tham gia vào hệ thống real-time
1. Laravel:(BE)
Nơi sinh ra sự kiện (event), xử lý logic và "phát sócng" ra ngoài

2. Broadcasting:(BE)
Cơ chế Laravel dùng để phát sóng sự kiện ra ngoài

3. Pusher:(trung gian bên thứ 3 tương tự google console chẳng hạn)
Dịch vụ trung gian gửi sự kiện từ laravel đến trình duyệt (qua WebSocket)

4. Laravel Echo: (FE)
Thư viện Javascript ở frontend, nghe các sự kiện được Pusher gửi đến.
