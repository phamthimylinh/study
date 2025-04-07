# 1. Pusher
- Pusher là một dịch vụ **gửi dữ liệu thời gian thực** từ server đến client
- Nó hoạt động dựa trên WebSockets, giúp "đẩy" **dữ liệu từ server xuống client ngay khi có sự thay đổi**, mà không cần client phải "hỏi" server liên tục.

➡️ Tưởng tượng Pusher giống như một anh bưu tá siêu nhanh: cứ có tin nhắn mới (event), là ảnh phi ngay đến người nhận để thông báo liền


# 2. Laravel Echo
- Laravel Echo là thư viện Javascript được laravel phát triển để nghe các event mà laravel phát ra (thường qua Pusher hoặc các dịch vụ tương tự).

➡️ Tưởng tượng Laravel Echo như chiếc điện thoại thông minh của người dùng: nó bật sẵn, và khi có "cuộc gọi" (event từ server), nó sẽ ngay lập tực nhận cuộc gọi đó và hiển thị thông báo ngay cho nguời dùng.


# 3. Mối quan hệ giữa Laravel, Pusher và Echo
- Laravel phát một sự kiện (event) -> gửi qua Pusher
- Pusher truyền sự kiện đó tới client đang mở ứng dụng
- Laravel Echo (ở client) đang "lắng nghe", và nhận được sự kiện ngay lập tức.

