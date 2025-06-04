# 1. Conditional Clause
- Đôi khi bạn chỉ muốn 1 số query clauses được áp dụng cho 1 query base dựa trên 1 số điều kiện nhất định
- Ví dụ bạn chỉ muốn app dụng 1 câu lệnh `where` nếu giá trị đầu vào nhất định có trong HTTP request.
=> Có thể thực hiện được điêu này bằng cách sử dụng `when` method
```php
$role = $request->input('role');

$users = DB::table('users')
    ->when($role, function (Builder $query, string $role) {
        $query->where('role_id', $role);
    })
    ->get();
```
- Phương thức `when` chỉ thực thi hàm closure khi tham số đầu tiên là `true`. Nếu tham số đầu tiên là `false`, thì hàm closure sẽ không thực hiện
- Ví dụ bên trên, hàm closure sẽ được đưa vào phương thức `when` sẽ được thực hiện khi trường `role` có xuất hiện và được đánh giá là `true`.

- Bạn có thể truyền vào 1 closure khác làm đối số thứ 3 cho phương thức `when`. Hàm closure này sẽ thực hiện nếu đối số 1 trả về false.
- Để minh họa cách sử dụng tính năng này, chúng ta sẽ sử dụng nó để cấu hình thứ tự mặc định của truy vấn.
```php
$sortByVotes = $request->boolean('sort_by_votes');

$users = DB::table('users')
    ->when($sortByVotes, function (Builder $query, bool $sortByVotes) {
        $query->orderBy('votes');
    }, function (Builder $query) {
        $query->orderBy('name');
    })
    ->get();
```
