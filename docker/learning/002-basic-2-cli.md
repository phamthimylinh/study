# Làm quen với các lệnh cơ bản
Docker chủ yếu hoạt đọng qua dòng lệnh (CLI - command line interface), nên việc nắm vững các lệnh cơ bản sẽ giúp bạn điều khiển docker dễ dàng.
Nội dung flow
- Docker CLI cơ bản
- Làm việc với image
- Logs và kiểm tra

## 1. Docker CLI cơ bản
Đây là những lệnh bạn sẽ dùng thường xuyên để quản lý container. Hãy tưởng tượng container như một "con thú cưng" mà bạn nuôi trong máy tính: bạn có thể cho nó chạy, dừng, xem nó đang làm gì, hoặc "dọn dẹp" khi không cần nữa.
###  `docker run`: Chạy một container từ image.
- **Bản chất**: Lệnh này nói với Docker: " Hãy lấy một image và tạo một container từ nó, rồi chạy nó lên". Nếu image chưa có trên máy, Docker sẽ tự tải từ Docker hub.
- **Ví dụ**: `docker run hello-word`
  - Kết quả: Tải image `hello-world`, chạy container, in ra thông điệp chào mừng.
- **Mẹo nhớ**: Nghĩ "run" như "chạy ngay đi" - nó là cách khởi động mọi thứ trong Docker.
- **Thực hành**: Chạy `docker run ubuntu` (sẽ tải image ubuntu), nhưng nó sẽ thoát ngay vì chưa có lệnh cụ thể. Để tương tác, thử `docker run -it ubuntu bash` (mở shell trong container Ubuntu).

### `docker ps`: Xem danh sách container đang chạy
