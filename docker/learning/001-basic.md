# Kiến thức cơ bản về Docker
## 1.1 Docker là gì?
Docker là một nền tảng dùng để **container hóa** ứng dụng. Nhưng container là gì? Hãy tưởng tượng container giống như một chiếc hộp nhỏ gọn, bên trong chứa mọi thứ mà ứng dụng của bạn cần để chạy: mã nguồn (code), thư viện, công cụ, và cả môi trường (như hệ điều hành thu nhỏ). Điều đặc biệt là container rất nhẹ, nhanh, và có thể chạy trên bất kỳ máy tính nào có cài Docker, bất kể hệ điều hành gốc là gì.

- **Ví dụ thực tế**: Hãy tưởng tượng bạn có một chiếc máy pha cà phê đóng gói sẵn (container). Bạn chỉ cần cắm điện và bấm nút là có cà phê, không cần quan tâm đến việc lắp ráp máy hay chuẩn bị nguyên liệu từ đầu.

### So sánh với máy ảo (VM):
- **Máy ảo (VM)**:
  - Là một hệ điều hành hoàn chỉnh (guest OS) chạy trên một phần mềm giả lập (hypervisor) như VMware hay VirtualBox, nằm trên hệ điều hành thật (host OS).
  - Nặng hơn vì nó chạy cả một hệ điều hành riêng, tiêu tốn nhiều tài nguyên (CPU, RAM, dung lượng ổ cứng).
  - Ví dụ: Bạn chạy ubuntu trên Windows qua VirtualBox, nó sẽ chiếm vài GB ram và bộ nhớ.
- **Container**:
  - Không cần guest OS, mà chia sẻ kernel (lõi) của hệ điều hành host. Điều này làm container nhẹ hơn nhiều so với VM.
  - Chỉ chứa ứng dụng và các phụ thuộc của nó, không cần toàn bộ hệ điều hành.
  - Ví dụ: Bạn chạy một ứng dụng Python trong container trên linux, nó chỉ cần Python và thư viện, không cần cài lại hệ điều hành Linux bên trong.
- **So sánh trực quan**:
  - VM giống như một ngôi nhà riêng biệt với đầy đủ nội thất, tường, mái (guest OS).
  - Container giống như một căn phòng trong ngôi nhà lớn (host OS), chỉ cần đồ đạc cần thiết (ứng dụng + phụ thuộc).

=> Container = "Nhẹ, nhanh, chia sẻ tài nguyên". VM = "Nặng, độc lập, tốn tài nguyên".

---------

## 1.2 Tại sao dùng Docker?
Docker mang lại nhiều lợi ích mà bạn hiểu rõ, bạn sẽ thấy nó rất đỉnh. Đây là lý do chính:
### 1. Tính di động (Portability):
Bạn viết ứng dụng trên máy windows, đóng gói thành container, rồi chạy trên Linux hay macOS mà không cần chỉnh sửa gì. "It works on my machine" không còn là vấn đề nữa!
### 2. Hiệu suất (Performance):
Vì nhẹ hơn VM, container khởi động nhanh, tiết kiệm tài nguyên (CPU, RAM).
### 3. Tái sử dụng (Reusability):
Một container có thể được dùng lại nhiều lần, hoặc chia sẻ cho người khác qua Docker Hub (như github cho container).
### 4. Quản lý dễ dàng
Bạn có thể chạy hàng trăm container trên một máy, mỗi container là một ứng dụng riêng biệt, không xung đột lẫn nhau.

### Ví dụ thực tế
Bạn làm một app web bằng Python, cần Flask và Redis. Thay vì cài đặt từng thứ trên máy, bạn dùng Docker để đóng gói tất cả thành một container, chạy được trên bất kỳ máy nào.

---------

## 1.3 Các thành phần chính của Docker
Để hiểu bản chất Docker, bạn cần nắm rõ các "nhân vật chính" trong hệ sinh thái này:

### 1. Docker Engine
- Là "trái tim" của Docker, bao gồm tất cả công cụ để tạo, chạy, quản lý container:
- Gồm 2 phần chính:
  - **Docker Client**: Nơi gõ lệnh (như `docker run`).
  - **Docker Daemon**: "Nhân viên" làm việc ngầm, thực hiện lệnh từ Client (tạo container, tài image,...).

### 2. Docker Client
Là cách bạn giao tiếp với Docker thông qua terminal (CLI). Ví dụ `docker ps` để xem danh sách container đang chạy.

### 3. Docker Daemon
Chạy ngầm trên máy, lắng nghe lệnh từ Client và thực hiện (như khởi độc container, tài image từ Registry).

### 4. Container
Là "thùng chứa" chạy ứng dụng. Mỗi container được tạo ra từ một **Image**.

### 5. Image
- Là bản "ảnh chụp" của ứng dụng + môi trường. Image giống như "bản thiết kế" để tạo ra container.
- Ví dụ: Image của Ubuntu có Python cài sẵn. Bạn dùng image này để tạo container chạy app Python.

### 6. Registry 
- Là kho lưu trữ image. Docker hub là registry phổ biến nhất, nơi bạn tài hoặc đẩy image lên.
- Ví dụ: `docker pull ubuntu` tài image Ubuntu từ Docker hub.
- **Hình dung**: Image giống như file cài đặt game (setup.exe), container là game đang chạy, còn registry là nơi bạn tài file đó.

## 1.4 Cài đặt docker
Để cài đặt trên window
- Cài đặt docker desktop từ trang chủ docker.com
- Cài đặt theo hướng dẫn (cần bật virtualization trong BIOS nếu dùng Windows)
- Mở terminal, chạy lệnh `docker --version`.
