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

# 2. Raw expressions: raw query
- Sẽ có một vài trường hợp bạn cần truyền một câu truy vấn raw query vào câu truy vấn của bạn.
- Để tạo một chuỗi raw expression, có thể dùng phương thước `raw()` của `DB` facade.
```php
$users = DB::table('users')
    ->select(DB::raw('count(*) as user_count, status'))
    ->where('status', '<>', 1)
    ->groupBy('status')
    ->get();
```
- **Lưu ý**: Các truy vấn raw sẽ được gắn vào câu query dưới dạng string nên cần cẩn thận để tránh tấn công SQL injection. 

## 2.1 Raw methods
- Thay vì dùng `DB::raw()`, có thể dùng các phương thức sau để chèn các câu truy vấn raw query vào câu truy vấn của bạn
- Hãy nhớ rằng Laravel không thể đảm bảo rằng bất kỳ câu truy vấn raw nào đều được bảo vệ khỏi tấn công SQL injection.

### 2.1.1 `selectRaw()`
- Phương thức `selectRaw()` có thể được sử dụng để thay thế cho `addSelect(DB::raw(...))`
- Phương thức này gồm 2 tham số: câu truy vấn raw query và mảng các  giá trị tham số ràng buộc (binding) cho câu truy vấn
```php
$orders = DB::table('orders')
    ->selectRaw('price * ? as price_with_tax', [1.0825])
    ->get();
```
### 2.1.2 `whereRaw()` & `orWhereRaw()`
- Phương thức `whereRaw()` và `orWhereRaw()` cho phép bạn thêm một mệnh đề where raw query vào câu truy vấn của bạn.
- 