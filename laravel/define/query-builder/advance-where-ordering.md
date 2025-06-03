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
### 1.1 Phương thức `orderBy`
- Phương thức `orderBy` cho phép bạn sắp xếp kết quả truy vấn theo một cột nhất định.
- Đối số đầu tiên của phương thức `orderBy` chỉ chấp nhận tên cột mà bạn muốn sắp xếp, trong khi đối số thứ 2 xác định hướng sắp xếp có thể là tăng hoặc giảm dần
```php
$users = DB::table('users')
    ->orderBy('name', 'desc')
    ->get();
```
- Để sắp xếp theo nhiều cột, bạn chỉ cần gọi `orderBy` nhiều lần
```php
$users = DB::table('users')
    ->orderBy('name', 'desc')
    ->orderBy('email', 'asc')
    ->get();
```

### 1.2 Phương thức `lastest` và `oldest`
- Phương thức `lastest` và `oldest` cho phép bạn dễ dàng sắp xếp kết quả theo ngày.
- Mặc định, kết quả sẽ trả về được sắp xếp theo cột `created_at`. Hoặc bạn có thể truyền theo tên cột mà bạn muốn sắp xếp theo.
```php
$user = DB::table('users')
    ->latest()
    ->first();
```

### 1.3 Random ordering
- Phương thức `inRandomOrder` có thể được sử dụng để sắp xếp ngẫu nhiên kết quả truy vấn. Ví dụ, bạn có thể sử dụng phương thức này để lấy ngẫy nhiên 1 nguời dùng
```php
$randomUser = DB::table('users')
    ->inRandomOrder()
    ->first();
```

### 1.4 Removing Existing Orderings
- Phương thức `reorder` xóa tất cả các mệnh đề "order by" đã được áp dụng trước đó
```php
$query = DB::table('users')->orderBy('name');

$unorderedUsers = $query->reorder()->get();
```
- Bạn có thể truyền 1 cột và redirect khi gọi phương thức `reorder` để xóa tất cả các mệnh đề  "order by" và áp dụng một thứ tự mới cho truy vấn
```php
$query = DB::table('users')->orderBy('name');

$usersOrderedByEmail = $query->reorder('email', 'desc')->get();
```

## 2. Grouping
### 2.1 Phương thức `groupBy` và phương thức `having`
- Phương thức `groupBy` và phương thức `having` có thể được sử dụng để nhóm các kết quả truy vấn
- Phương thức `having` tương tự như phương thức `where`
```php
$users = DB::table('users')
    ->groupBy('account_id')
    ->having('account_id', '>', 100)
    ->get();
```
- Có thể sử dụng phương thức `havingBetween` để lọc kết quả trong một phạm vi nhất định
```php
$report = DB::table('orders')
    ->selectRaw('count(id) as number_of_orders, customer_id')
    ->groupBy('customer_id')
    ->havingBetween('number_of_orders', [5, 15])
    ->get();
```
- Bạn có thể truyền nhiều đối số cho phương thức `groupBy` để nhóm theo nhiều cột
```php
$users = DB::table('users')
    ->groupBy('first_name', 'status')
    ->having('account_id', '>', 100)
    ->get();
```
- Để xây dựng moojg mệnh đề `having` nâng cao hơn, hãy tìm hiểu phương thức `havingRaw`.

### 2.2 Limit và Offset
- Phương thức `skip` và `take` dùng để giới hạn kết quả trả về từ truy vấn hoặc bỏ qua một số lượng kết quả nhất định trong query
```php
$users = DB::table('users')->skip(10)->take(5)->get();
```
- Ngoài ra, có thể sử dụng phương thức `limit` hoặc `offset`, 2 phương thức này tương tự như `skip` và `take`
```php
$users = DB::table('users')
    ->offset(10)
    ->limit(5)
    ->get();
```
