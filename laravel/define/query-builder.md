# 1. Giới thiệu
- Query builder của laravel sử dụng PDO parameter binding giúp bảo vệ các cuộc tấn công SQL injection

# 2. Chạy database queries
## 2.1 Lấy tất cả dữ liệu từ 1 bảng
- Có thể dùng phương thức `table()` của `DB` facade để bắt đầu truy vấn
- `table()` returns một query builder instance cho bảng được truy vấn và lấy ra kết quả bằng phương thức `get()`

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Support\Facades\DB;
use Illuminate\View\View;

class UserController extends Controller
{
    /**
     * Show a list of all of the application's users.
     */
    public function index(): View
    {
        $users = DB::table('users')->get();

        return view('user.index', ['users' => $users]);
    }
}
```

- `get()` trả về 1 instance của `Illuminate\Support\Collection` chứa kết quả của truy vấn, trong đó mỗi kết quả là một instance của PHP `stdClass` object 

## 2.2 Trả về một bản ghi dữ liệu
- Nếu bạn cần trả về chỉ 1 dòng từ 1 bảng trong csdl, bạn có thể dùng method first() của DB facade. Phương thức này sẽ trả về một object `stdClass`

```php
$user = DB::table('users')->where('name', 'John')->first();

return $user->email;
``` 
## 2.3 Trả về một mảng các giá trị 
 - Có thể dùng `pluck()` của `Collection` để trả về 1 mảng giá trị 
 ```php
 use Illuminate\Support\Facades\DB;

$titles = DB::table('users')->pluck('title');

foreach ($titles as $title) {
    echo $title;
}
```
- hoặc có thể lấy gía trị nhiều cột 
```php
$titles = DB::table('users')->pluck('title', 'name');

foreach ($titles as $name => $title) {
    echo $title;
}
```

# 3. Chia nhỏ từng đoạn kết quả
- Khi làm việc với hàng ngàn bản ghi trong csdl, hãy cân nhắc sử dụng `chunk()` của `DB` facade.
- Phương thức này lấy ra một phần nhỏ kết quả tại 1 thời điểm và đưa ra từng khối vào func closure để xử lý.
- Ví dụ, lấy một bảng dữ liệu users theo từng phần, mỗi phần 100 record cùng một lúc
```php
use Illuminate\Support\Collection;
use Illuminate\Support\Facades\DB;

DB::table('users')->orderBy('id')->chunk(100, function (Collection $user) {
    foreach ($users as $user) {
        // ....
    }
})
```
- Có thể dùng xử lý chunk bằng cách return `false` từ closure func:
```php
DB::table('users')->orderBy('id')->chunk(100, function (Collection $users) {
    // Process the records...

    return false;
});
```
- Nếu như bạn đang update dữ liệu trong khi chunk kết quả, kết quả chunk có thể trả về những dữ liệu không mong muốn -> nên sử dụng `chunkById`, phương thức này sẽ tự động phân tra kết quả dựa trên primary key:
```php
DB::table('users')->where('active', false)
    ->chunkById(100, function (Collection $users) {
        foreach ($users as $user) {
            DB::table('users')
                ->where('id', $user->id)
                ->update(['active' => true]);
        }
    });
```
- Với `chunkId` và `lazyById` thêm các điều kiện `where` vào truy vấn của riêng chúng rồi

# 4. Lazy method: Trả về kết quả trực tiếp lazy
- `lazy()` làm việc tương tự `chunk()`, nghĩa là nó thực hiện truy vấn theo từng phần. 
- Tuy nhiên, thay vì truyền vào 1 callback func, `lazy()` trả về 1 `LazyCollection`, cho phép bạn tương tác với nó như một collection bình thường.

```php
use Illuminate\Support\Facades\DB;

DB::table('users')->orderBy('id')->lazy()->each(function (object $user) {
    // ...
});
```
- Tương tự như `chunk()`, nếu muốn update dữ liệu trong khi lặp dùng `lazy` thì giải pháp tốt nhất là dùng `lazyById` hoặc `lazyByIdDesc`, các phương thức này sẽ tự động phân trang kết quả dựa trên primary key

```php
DB::table('users')->where('active', false)
    ->lazyById()->each(function (object $user) {
            DB::table('users')
                ->where('id', $user->id)
                ->update(['active' => true]);
    });
```
- Khi thực hiện update hoặc delete dữ liệu trong khi lặp, hay bất kỳ thay đổi nào đối với primary hoặc foreign key đều có thể ảnh hưởng đến truy vấn chunk hay lazy, dẫn đến việc bản ghi không bao gồm vào kết quả trả về

# 5. Hàm tổng hợp - Aggregates
- Query builder cũng cung cấp đa dạng các hàm cho việc lấy các giá trị tổng hợp như: count, max, min, avg , sum.
- Có thể gọi chúng sau câu query của bạn
```php
use Illuminate\Support\Facades\DB;

$users = DB::table('users')->count();

$price = DB::table('orders')->max('price');
```
- Đương nhiên, bạn cũng có thể kết hợp các hàm này với các điều kiện để tinh chỉnh các giá trị của bạn để tính toán.
```php
$price = DB::table('orders')
    ->where('finalized', 1)
    ->avg('price');
```
- Các hàm xác định bản ghi có tồn tại hay không: 
    - Thay vì dùng các hàm count để đếm rồi xác định ⇒ có thể dùng `exists` và `doesnExist`.

```php
if (DB::table('orders')->where('finalized', 1)->exists()) {
    // ...
}

if (DB::table('orders')->where('finalized', 1)->doesntExist()) {
    // ...
}
 ```
 