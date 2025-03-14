# Overview
## 1. Kiến thức cơ bản về Docker
- **Docker là gì?:** Hiểu khái niệm container, sự khác biệt giữa container và máy ảo (VM).
- **Tại sao dùng docker?**: Lợi ích như tính di động, hiệu suất, và tái sử dụng.
- **Các thành phần chính của docker**:
    - Docker Engine
    - Docker Client
    - Docker Daemon
    - Container, Image, Registry
- **Cài đặt Docker:** Cách cài đặt trên các hệ điều hành phổ biến (Windows, macOS, Linux).

## 2. Làm quen với các lệnh cơ bản
- **Docker CLI cơ bản:**
    - `docker run`: Chạy một container từ image.
    - `docker ps`: Xem danh sách container đang chạy.
    - `docker stop / docker start`: Dừng và khởi động container.
    - `docker rm / docker rmi`: Xóa container và image.
- **Làm việc với image**:
    - `docker pull`: Tải image từ Docker Hub.
    - `docker images`: Xem danh sách image.
- **Logs và kiểm tra**: `docker logs`, `docker inspect`.

## 3. Hiểu và xây dựng Docker Image
- **Dockerfile là gì?**: Cấu trúc và cú pháp của Dockerfile.
- **Các lệnh phổ biến trong Dockerfile:**
    - `FROM`: Chọn image cơ sở.
    - `RUN`: Chạy lệnh trong quá trình build.
    - `COPY` / `ADD`: Sao chép tệp vào image.
    - `CMD` / `ENTRYPOINT`: Xác định lệnh mặc định khi container chạy.
- **Build image**: Sử dụng `docker build`.
- **Tối ưu hóa image**: Cách giảm kích thước image, sử dụng multi-stage build

## 4. Quản lý container
- **Tùy chỉnh container:**
    - Gắn cổng (port mapping): `-p`.
    - Gắn thư mục (volume): `-v`.
    - Đặt tên container: `--name`.
- **Chạy container ở chế độ nền**: Sử dụng `-d` (detached mode).
- **Tương tác với container:** `docker exec` để chạy lệnh bên trong container.

## 5. Làm việc với mạng trong Docker (Docker Networking)
- **Các loại mạng mặc định**: Bridge, Host, None.
- **Tạo mạng tùy chỉnh**: `docker network create`.
- **Kết nối container**: Cách các container giao tiếp với nhau qua mạng

## 6. Quản lý dữ liệu trong Docker (Docker Volumes)
- **Volumes vs Bind Mounts:** Sự khác biệt và cách sử dụng.
- **Tạo và quản lý volume**: `docker volume create`, `docker volume ls`.
- **Lưu trữ dữ liệu bền vững**: Ứng dụng thực tế với cơ sở dữ liệu.

## 7. Docker Compose
-** Docker Compose là gì?**: Công cụ để quản lý nhiều container.
- **Cấu trúc file** `docker-compose.yml`
    - Dịch vụ (services), mạng (networks), volumes.
- **Các lệnh cơ bản:**
    - `docker-compose up`: Khởi động các dịch vụ.
    - `docker-compose down`: Dừng và xóa.
- **Ví dụ thực tế**: Chạy ứng dụng web (ví dụ: Nginx + Python).

## 8. Docker Registry
-** Docker Hub**: Đăng ký tài khoản, push/pull image.
- **Tự host registry**: Cách thiết lập registry riêng.
- **Quản lý image**: Tag và version image.

## 9. Docker trong thực tế (Advanced Topics)
- **Tối ưu hóa hiệu suất**: Debug container, giới hạn tài nguyên (`--memory`, `--cpu`).
- **Bảo mật trong Docker**: Quét lỗ hổng image, chạy container với user không phải root.
- **Docker Swarm**: Cụm container cơ bản (container orchestration).
- **Kết hợp với CI/CD**: Sử dụng Docker trong pipeline (GitHub Actions, Jenkins).

## 10. Thực hành dự án
- **Dự án nhỏ**:
    - Chạy một ứng dụng web đơn giản (ví dụ: Flask + Nginx).
    - Container hóa cơ sở dữ liệu (MySQL, PostgreSQL).
- **Dự án lớn:**
    - Xây dựng một hệ thống microservices với Docker Compose.
    - Triển khai ứng dụng lên cloud (AWS, GCP) bằng Docker.

## 11. Tài liệu và cộng đồng
- **Đọc tài liệu chính thức**: Docker Documentation (docs.docker.com).
- **Tham gia cộng đồng**: Stack Overflow, Reddit, diễn đàn Docker.
- **Học qua ví dụ**: Tìm các dự án mẫu trên GitHub.

# Lộ trình học
- **Tuần 1-2**: Làm quen với Docker cơ bản (đầu mục 1-2).
- **Tuần 3-4**: Học cách tạo image và quản lý container (đầu mục 3-4).
- **Tuần 5-6**: Tìm hiểu mạng và volumes (đầu mục 5-6).
- **Tuần 7-8**: Làm chủ Docker Compose và Registry (đầu mục 7-8).
- **Tuần 9 trở đi**: Áp dụng nâng cao và thực hành dự án (đầu mục 9-10).

