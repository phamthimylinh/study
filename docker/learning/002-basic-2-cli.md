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
- **Bản chất**: Giống như "danh sách thú cưng đang hoạt động". Chỉ hiển thị container đang chạy không hiển thị những container đã dừng
- **Ví dụ**: `docker ps`
  - Nếu không có container nào chạy, bảng sẽ trống
- **Thêm**: dùng `docker ps -a` để xem tất cả container (đang chạy + đã dừng).
- **Mẹo nhớ**: "ps" giống "process status" - trạng thái các tiếng trình đang sống.

### `docker stop` / `docker start`: Dừng và khởi động container.
- **Bản chất**:
  - `docker stop`: "Tạm dừng" container, nhưng không xóa nó, giống như cho thú cưng ngủ.
  - `docker start`: "Đánh thức" container đã dùng để nó chạy lại.
- **Ví dụ**: 
  1. Chạy `docker run -d ubuntu sleep 100` (container chạy nền 100 giây).
  2. Xem ID bằng `docker ps`.
  3. Dừng: `docker stop <ID>`.
  4. Khởi động lại: `docker start <ID>`.
- Mẹo nhớ: "stop" = dừng, "start" = khởi động - đơn giản như bật/tắt công tắc

### `docker rm` / `docker rmi`: Xóa container và image.
- **Bản chất**: 
  - `docker rm`: Dọn dẹp container đã dừng, giống như "dọn chuồng thú cưng".
  - `docker rmi`: Xóa image, giống như xóa "bản mẫu" của container.
- **Ví dụ**:
  - `docker ps -a`: lấy ID container đã dừng -> `docker rm <ID>`.
  - `docker image`: Lấy tên image -> `docker rmi <tên-image>`.
- **Lưu ý**: Không xóa được container đang chạy (phải `stop` trước) hoặc image đang được container dùng.
- **Mẹo nhớ**: "rm" = remove, "rmi" i trong image

## 2. Làm việc với image
Image là "bản thiết kế" của container, giống như công thức nấu ăn vậy. Bạn cần biết cách tải và quản lý chúng.
### `docker pull`: Tải image từ docker hub
- **Bản chất**: lệnh này giống như "đi chợ mua nguyên liệu" - tải image về máy dùng sau.
- **Ví dụ**: `docker pull nginx`
  - Kết quả: Tải image Nginx (web server) từ docker hub.
- **Mẹo nhớ**: "pull" = kéo về, như kéo một món đồ từ xa.

### `docker image`: Xem danh sách image.
- ** Bản chất**: Kiểm tra "kho nguyên liệu" của bạn, liệt kê tất cả các image đang có.
- ** Ví dụ**: `docker image`
  - Kết quả: Hiển thị teen, tag (phiên bản), kích thước image.
- **Mẹo nhớ**: Nghĩ "images" như danh sách "ảnh chụp" của các công thức.

## 3. Logs và kiểm tra
Khi container chạy, bạn cần biết nó đang làm gì. Đây là cách "trò chuyện" với container.
### `docker logs`: Xem nhật ký của container.
- **Bản chất**: Giống như đọc nhật ký của thú cưng - xem nó đã làm gì, lỗi gì.
- ** Ví dụ**:
  - 1. Chạy `docker run -d ubuntu echo "xin chao"`.
  - 2. Lấy ID từ `docker ps`.
  - 3. Xem logs: `docker log <ID>` -> thấy "xin chao".
- **Mẹo nhớ**: "logs" = ghi chép, như sổ tay hoạt động.
### `docker inspect`: Kiểm tra chi tiết container/image.
- **Bản chất**: Giống như "khám sức khỏe" - cung cấp mọi thông tin về container/image (IP, cổng, trạng thái...).
- **Ví dụ**: `docker inspect <ID>`
  - Kết quả: trả về dữ liệu dạng json với thông tin chi tiết.
- **Mẹo nhớ**: "inspect" = kiểm tra kỹ lưỡng, như nhìn sâu vào bên trong.
