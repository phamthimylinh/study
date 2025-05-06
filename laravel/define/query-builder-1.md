# 1. Câu lệnh select
## 1.1 Chỉ định một mệnh đề select
- Không phải lúc nào cũng cần lấy ra tất cả các cột trong cơ sở dữ liệu
- Có thể dùng `select()` để tùy chỉnh các trường cần lấy ra cho câu truy vấn.
```php
use Illuminate\Support\Facades\DB;

$users = DB::table('users')
    ->select('name', 'email as user_email')
    ->get();
```
- Phương thức `distinct()` cho phép trả về các kết quả không trùng nhau
```php
$users = DB::table('users')
    ->distinct()
    ->get();
```
- Nếu bạn đã có một câu truy vấn rồi, mà sau đó muốn lấy thêm 1 cột dữ liệu vào câu truy vấn select trước đó => dùng phương thức `addSelect()`
```php
$query = DB::table('users')->select('name');

$users = $query->addSelect('age')->get();
```
