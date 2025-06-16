# Intro
- Laravel bao gồm cả eloquent, một object-relational mapper, cái mà giúp bạn cảm thấy thú vị khi tương tác với csdl.
- Khi sử dụng Eloquent, mỗi bảng trong csdl tương ứng với 1 "Model" cái mà được sử dụng để tương tác với bảng đó
- Ngoài việc truy xuất bản ghi từ csdl, mô hình Eloquent còn cho phép insert, update, delete các bản ghi trong bảng
- Note: 
+ Trước khi bắt đầu, hãy chắc chắn rằng cấu hình kết nối với csdl trong file `config/database.php` trong  hệ thống của bạn.
+ Để biết thêm thông tin về cách cấu hình csdl, hãy tham khảo tài liệu [cấu hình csdl](https://laravel.com/docs/12.x/database#configuration).

# Configuration
- Cấu hình cho các dịch vụ csdl của Laravel nằm ở `config/database.php`. Trong file này, bạn có thể định nghĩa tất cả các connect vào csdl, cũng như chỉ định kết nối vào csdl nào sẽ được sử dụng mặc định
- Hầu hết các tùy chọn cấu hình trong file này đều được control từ các biến trong file .env. File này cung cấp ví dụ về hầu hết các hệ thống csdl được laravel hỗ trợ 

Mặc định, Laravel đã cấu hình sẵn sàng để sử dụng, Cấu hình docker để pháp triển với các ứng dụng trên Laravel. Tuy nhiên, bạn có thể tự do sửa đổi cấu hình csdl theo nhu cầu của csdl local của bạn

## 1 SQLite Configuration
- CSDL SQLite tồn tại ở 1 file duy nhất trên hệ thống của bạn. Có thể tạo csdl SQLite mới bằng lệnh `touch` trong terminal `touch database/database.sqlite`. Sau đó csdl đã được tạo, bạn có thể dễ dàng cấu hình biến môi trường của mình bằng cách đặt đường dẫn tuyệt đối (absolute path) trong biến DB_DATABASE của env
```php
DB_CONNECTION=sqlite
DB_DATABASE=/absolute/path/to/database.sqlite
```
- Theo mặc định, các ràng bụôc khóa ngoại được bật cho các kết nối SQLite. Nếu bạn muốn tắt chúng, bạn nên đặt biến môi trường DB_FOREIGN_KEYS thành false
```php
DB_FOREIGN_KEYS=false
```
- Note: mặc định laravel dùng SQLite để làm csdl, và sẽ sinh ra file database/database.sqlite.

## 2 Microsoft SQL Server configuration
- Để sử dụng csdl Microsoft Sql server, bạn phải đảm bảo rằng mình đã cài đặt php extension `pdo_sqlsrv` và `sqlsrv` cũng như tất cả các phụ thuộc vào Microsoft SQL ODBC yêu cầu

### Configuration Using URLs
- Thông thường, kết nối với csdl được cấu hình bằng nhiều giá trị như `host, database, username, password` etc,
- Mỗi giá trị cấu hình này đều có biến env riêng. Điều này có ý nghĩa là khi cấu hình thông tin kết nối csdl của bạn trên máy chủ và bạn cần quản lý biến môi trường
- Một số hệ quản trị csdl được quản lý như AWS và Heroku cung cấp 1 url csdl duy nhất chứa tất cả các thông tin kết nối trong 1 chuỗi duy nhất.
```php
mysql://root:password@127.0.0.1/forge?charset=UTF-8
```
- Các Url này sẽ tuân thủ theo quy ước chuẩn
```php
driver://username:password@host:port/database?options
```
