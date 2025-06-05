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
# 2. Insert statements
- query builder cũng cung cấp một phương thức `insert` cái mà dùng để chèn nhiều bản ghi vào trong bảng của csdl
- Phương thức `insert` method chấp nhận 1 mảng các tên cột và các giá trị;
```php
DB::table('users')->insert([
    'email' => 'kayla@example.com',
    'votes' => 0
]);
```
- Bạn có thể chèn nhiều bản ghi cùng một lúc bằng các truyền một mảng các mảng, mỗi mảng đại diện cho 1 bản ghi cái mà cần được chèn vào bảng
```php
DB::table('users')->insert([
    ['email' => 'picard@example.com', 'votes' => 0],
    ['email' => 'janeway@example.com', 'votes' => 0],
]);
```
- Phương thức `insertOrIgnore` sẽ bỏ qua các lỗi khi chèn bản ghi vào csdl. Khi dùng phương thức này, bạn nên biết reawfng rỗi bản ghi trùng lặp sẽ bị bỏ qua và các loại lỗi kháccuxng có thể bị bỏ qua tuỳ thuộc vào công cụ csdl.
- Ví dụ phương thức `insertOrIgnore` sẽ bỏ qua chế độ `MySQL's strict mode`
```php
DB::table('users')->insertOrIgnore([
    ['id' => 1, 'email' => 'sisko@example.com'],
    ['id' => 2, 'email' => 'archer@example.com'],
]);
```

- Phương thức `inserUsing` sẽ chèn các bản ghi mới vào trong khi sử dụng truy vấn phụ để xác định dữ liệu cần chèn
```php
DB::table('pruned_users')->insertUsing([
    'id', 'name', 'email', 'email_verified_at'
], DB::table('users')->select(
    'id', 'name', 'email', 'email_verified_at'
)->where('updated_at', '<=', now()->subMonth()));
```
## Auto-Increamenting IDs
Nếu bảng có id tự động tăng, hãy sử dụng phương thức `insertGetId` để chèn bản ghi và sau đó trả về id"
```php
$id = DB::table('users')->insertGetId(
    ['email' => 'john@example.com', 'votes' => 0]
);
```
- Khi dùng PostgreSql, phương thức `insertGetId` mong đợi cột tự động tăng và được đặt tên là id, nếu bạn muốn lấy id từ 1 chuỗi khác, bạn có thể truyền tên cột làm thao số thứ 2 cho phương thức `insertGetId`

## Upsert
- Phương thức `upsert` sẽ thêm mới các bản ghi chưa tồn tại và cập nhật các bản ghi đã tồn tại mà bạn chỉ định
- Tham số đầu tiên của phương thức bao gồm các giá trị để chèn hoặc cập nhật, trong khi tham số thứ 2 là danh sách các cột xác định tính unique của các bản ghi trong bảng liên kết.
Tham số thứ 3 (tham số cuối) là một mảng các cột cần cập nhật (nếu bản ghi khớp và đã tồn tại trong csdl)
```php
DB::table('flights')->upsert(
    [
        ['departure' => 'Oakland', 'destination' => 'San Diego', 'price' => 99],
        ['departure' => 'Chicago', 'destination' => 'New York', 'price' => 150]
    ],
    ['departure', 'destination'],
    ['price']
);
```
- Ở ví dụ bên trên, Laravel sẽ cố gắng chèn 2 bản ghi, nếu một bản ghi đã tồn tại với cùng giá trị của cả 2 cột `departure` và `destination`, laravel sẽ update bản ghi đó với giá trị cột "price"

- Lưu ý: Tất cả các csdl ngoại trừ SQL server đều yêu cầu đối số thứ 2 trong phương thức `upsert` là phải có một chỉ mục "primary" hoặc "unique". Ngoài ra MariaDB và MySql bỏ qua đối số thứ 2 của phương thức `upsert` và luôn sử dụng chỉ mục "primary" và "unique" của bảng để pháp hiện các bản ghi tồn tại

# 3. Update statements
