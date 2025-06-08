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
Ngoài việc chèn các bản ghi vào csdl, query builder cũng có thể update các bản ghi hiện có bằng phương thức `update`.
Phương thức `update` giống như các phương pháp chèn, chấp nhận một mảng các cặp (cột và giá trị) cần cập nhật, và trả về các cột đã cập nhật.
Bạn có thể hạn chế truy cập bằng cách sử dụng mệnh đề `where`
```php
$affected = DB::table('users')
    ->where('id', 1)
    ->update(['votes' => 1]);
```
## 3.1 update or insert
- Đôi khi bạn cần update 1 bản ghi trong csdl hoặc tạo mới nó nếu nó không tồn tại => phương thức `updateOrInsert` có thể được sử dụng
- Phương thức `updateOrInsert` chấp nhận 2 đối số: 1 mảng các điều kiện để tìm ra bản ghi và 1 mảng các cặp key value cần update

- Phương thức `updateOrInsert` sẽ cố gắng tìm bản ghi phù hợp trong csdl bằng cách dùng các cặp giá trị của đối số đầu tiên. Nếu có bản ghi tồn tại, nó sẽ update với các giá trị ở đối số thứ 2. Nếu không tìm thấy bản ghi, một bản ghi mới sẽ được thêm với các thuộc tính của đối số thứ 2

```php
DB::table('users')
    ->updateOrInsert(
        ['email' => 'john@example.com', 'name' => 'John'],
        ['votes' => '2']
    );
```
- Bạn có thể cung cấp 1 hàm closure cho phương thức `updateOrInsert` để tuỳ chỉnh các thuộc tính cái mà được cập nhật hoặc chèn vào csdl dựa trên sự tồn tại của 1 bản ghi khớp
```php
DB::table('users')->updateOrInsert(
    ['user_id' => $user_id],
    fn ($exists) => $exists ? [
        'name' => $data['name'],
        'email' => $data['email'],
    ] : [
        'name' => $data['name'],
        'email' => $data['email'],
        'marketable' => true,
    ],
);
```

## 3.2 Updating JSON columns
- Khi cập nhật một cột JSON, bạn nên sử dụng cú pháp `->` để cập nhật khoá thích hợp trong đối tượng JSON. Hành động này được hỗ trợ trên MariaDB 10.2+, MySQL 5.7 và PostGreSql 9.5+
```php
$affected = DB::table('users')
    ->where('id', 1)
    ->update(['options->enabled' => true]);
```
### 3.2.1 Increment and Decrement
- query builder cũng cung cấp các phương thức để tăng hoặc giảm giá trị của cột nhất định, Cả hai phương thức này đều chấp nhận ít nhất 1 đối số là cột cần tăng hoặc giảm, đối số thứ 2 là để chỉ định số lượng cột cần tăng hoặc giảm  

```php
DB::table('users')->increment('votes');

DB::table('users')->increment('votes', 5);

DB::table('users')->decrement('votes');

DB::table('users')->decrement('votes', 5);
```
- Nếu cần , bạn cũng có thể chỉ định các cột bổ sung để cập nhật giá trị trong quá trình tăng hoặc giảm
```php
DB::table('users')->increment('votes', 1, ['name' => 'John']);
```
- ngoài ra, bạn có thể tăng hoặc giảm nhiều cột cùng lúc bằng cách sử dụng phương thức `incrementEach` và `decrementEach`
```php
DB::table('users')->incrementEach([
    'votes' => 5,
    'balance' => 100,
]);
```
**Note**:
- Hai phương thương thức này chỉ áp dụng cho các cột có kiểu dữ liệu `interger`, `float`, `decimal`
- đối số: 
+ 1 là cột cần tăng hoặc giảm
+ 2 là số lượng tăng hoặc giảm
+ 3 là mảng các cột khác muốn cập nhật cùng lúc

# 4. Delete Statements
- Phương thức `delete` của query builder có thể được dùng để xoá các cột trong bảng, phương thức `delete` trả về số lượng row bị ảnh hưởng,
- Bạn có thể hạn chế câu lệnh `delete` bằng việc gọi thêm `where` trước khi `delete`
```php
$deleted = DB::table('users')->delete();

$deleted = DB::table('users')->where('votes', '>', 100)->delete();
```
