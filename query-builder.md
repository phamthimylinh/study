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


