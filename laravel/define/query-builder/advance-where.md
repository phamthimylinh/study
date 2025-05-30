# 1. where exists clauses
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
```
select * from users
where exists (
    select 1
    from orders
    where orders.user_id = users.id
)
```

# 2. Subquery where clauses
