# Kiến thức cơ bản về Docker
## 1.1 Docker là gì?
Docker là một nền tảng dùng để **container hóa** ứng dụng. Nhưng container là gì? Hãy tưởng tượng container giống như một chiếc hộp nhỏ gọn, bên trong chứa mọi thứ mà ứng dụng của bạn cần để chạy: mã nguồn (code), thư viện, công cụ, và cả môi trường (như hệ điều hành thu nhỏ). Điều đặc biệt là container rất nhẹ, nhanh, và có thể chạy trên bất kỳ máy tính nào có cài Docker, bất kể hệ điều hành gốc là gì.

- **So sánh với máy ảo (VM)**:
    - Máy ảo (VM): Chạy toàn bộ hệ điều hành (OS) riêng biệt trên phần cứng ảo hóa -> Nặng, tốn tài nguyên.
    - Container: Chỉ dùng kernel của hệ điều hành host, không cần OS riêng -> Nhẹ, khởi động nhanh (thường chỉ vài giây).
    - Ví dụ: Nếu bạn muốn chạy một ứng dụng Python, VM cần cả OS (như ubuntu) + Python, còn container chỉ cần Python và thư viện cần thiết.

## 1.2 Tại sao dùng Docker?
