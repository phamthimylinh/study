# where advance
## 1. where exists clauses
- Phương thức `whereExists` cho phép bạn viết mệnh đề `SQL` nếu tồn tại.
- Phương thức này chấp nhận một hàm closure, hàm này sẽ nhận vào một query builder instance, cho phép bạn xác định truy vấn sẽ được đặt bên trong mệnh đề  "exists"
```php
$users = DB::table('users')
    ->whereExists(function (Builder $query) {
        $query->select(DB::raw(1))
            ->from('orders')
            ->whereColumn('orders.user_id', 'users.id');
    })
    ->get();
```
- Ngoài ra, bạn có thể cung cấp một query object cho phương thức `whereExists` thay vì 1 hàm closure:
```php
$orders = DB::table('orders')
    ->select(DB::raw(1))
    ->whereColumn('orders.user_id', 'users.id');

$users = DB::table('users')
    ->whereExists($orders)
    ->get();
``
- Cả 2 cách trên đều tạo ra một câu SQL dạng:
```php
select * from users
where exists (
    select 1
    from orders
    where orders.user_id = users.id
)
```

## 2. Subquery where clauses
- Đôi khi cần truy vấn `where` để so sánh kết quả của một truy vấn phụ với 1 giá trị nhất định, bạn có thể thực hiện điều này bằng cách truyền vào 1 closure và một giá trị cho phương thứ where.
- Ví dụ truy vấn sau sẽ lấy tất cả người dùng có thành viên gần đây của một loại nhất định
```php
use App\Models\User;
use Illuminate\Database\Query\Builder;

$users = User::where(function (Builder $query) {
    $query->select('type')
        ->from('membership')
        ->whereColumn('membership.user_id', 'users.id')
        ->orderByDesc('membership.start_date')
        ->limit(1);
}, 'Pro')->get();
```
- Hoặc bạn cũng có thể xây dựng mệnh đề `where` để so sánh 1 cột với 1 kết quả của 1 subquery
- Bạn có thể thực hiện điều này bằng cách truyền 1 cột, toán tử , và hàm closure cho phương thức `where`
- Ví dụ, truy vấn tất cả các bản ghi thu nhập có số tiền nhỏ hơn mức bình thường
```php
use App\Models\Income;
use Illuminate\Database\Query\Builder;

$incomes = Income::where('amount', '<', function (Builder $query) {
    $query->selectRaw('avg(i.amount)')->from('incomes as i');
})->get();
```

## 3. Full text where clauses
Full text where clause được hỗ trợ: MariaDB, MySql và PostgreSQL
- Phương thức `whereFullText` và `orWhereFullText` có thể được sử dụng để thêm full text `where` vào truy vấn cho các cột cái mà có chỉ mục full text
- Các phương pháp này sẽ được chuyển đổi thành SQL phù hợp cho hệ thống csdl cơ bản bởi laravel
- Ví dụ, mệnh đề MATCH AGAINST sẽ được generated cho các ứng dụng MariaDB hoặc MySql:
```php
$users = DB::table('users')
    ->whereFullText('bio', 'web developer')
    ->get();
```

# Ordering, grouping, limit and offset
## 1. Ordering
