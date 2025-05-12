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
- Phương thức này chấp nhận một mảng làm tham số thứ 2
```php
$orders = DB::table('orders')
    ->whereRaw('price > IF(state = "TX", ?, 100)', [200])
    ->get();
```
### 2.1.3 `havingRaw / orHavingRaw`
- Phương thức `havingRaw` và `orHavingRaw` cho phép cung cấp 1 raw làm giá trị cho mệnh đề "having".
- Phương thức này chấp nhận một mảng làm tham số thứ 2 của chúng
```php
$orders = DB::table('orders')
    ->select('department', DB::raw('SUM(price) as total_sales'))
    ->groupBy('department')
    ->havingRaw('SUM(price) > ?', [2500])
    ->get();
```

### 2.1.4 `orderByRaw`
- Phương thước này cung cấp 1 raw query vào mệnh đề "order by":
```php
$orders = DB::table('orders')
    ->orderByRaw('updated_at - created_at DESC')
    ->get();
```

### 2.1.5 `groupByRaw`
- Phương thức này cung cấp 1 raw query làm giá trị cho mệnh đề "group by"
```php
$orders = DB::table('orders')
    ->select('city', 'state')
    ->groupByRaw('city, state')
    ->get();
```

# 3. Join
## 3.1 Inner join
- Query builder cũng có thể dùng mệnh đề join, để thực hiện "inner join" basic, có thể dùng phương thức `join()` trên instance query builder.
- Tham số của phương thức join là tên bảng màn bạn muốn kết nối, tham số 2 là trường kết nối của bảng, tham số 3 là biểu thức kết nối = (có thể bỏ qua - tự hiểu là =), tham số 4 là trường kết nối của bảng.
- Có thể dùng nhiều phương thức join trên cùng 1 câu query
```php
use Illuminate\Support\Facades\DB;

$users = DB::table('users')
    ->join('contacts', 'users.id', '=', 'contacts.user_id')
    ->join('orders', 'users.id', '=', 'orders.user_id')
    ->select('users.*', 'contacts.phone', 'orders.price')
    ->get();
```

## 3.2 Left join / right join
- Nếu muốn thực hiện "left join" hoặc "right join" thay vì "inner join", dùng phương thức `leftJoin()` hoặc `rightJoin()`
- 2 Phương thức này có signature giống với phương thức join
```php
$users = DB::table('users')
    ->leftJoin('posts', 'users.id', '=', 'posts.user_id')
    ->get();

$users = DB::table('users')
    ->rightJoin('posts', 'users.id', '=', 'posts.user_id')
    ->get();
```
## 3.3 Cross join
- Có thể dùng phương thức `crossJoin()` để thực hiện "cross join" (nối chéo)
- Cross join tạo ra 1 sản phẩm thuộc phái, giữa bảng đầu tiên và bảng được ghép lại
```php
 $sizes = DB::table('sizes')
    ->crossJoin('colors')
    ->get();
```

## 3.4 Join nâng cao
- Bạn cũng có thể chỉ định mệnh đề join nâng cao hơn
- Bắt đầu với việc truyền 1 closure function vào tham số thứ 2 của `join()`
- Closure sẽ nhận 1 tham số đầu vào là `JoinClause $join` của `Illuminate\Database\Query\JoinClause` cái mà cho phép bạn chỉ định các ràng buộc trên "join"
```php
 DB::table('users')
    ->join('contacts', function (JoinClause $join) {
        $join->on('users.id', '=', 'contacts.user_id')->orOn(/* ... */);
    })
    ->get();
```
- Nếu muốn dùng `where` trong khi nối, thì có thể dùng phương thức `where` hoặc `orWhere` được cung cấp từ `JoinClause`
- Thay vì chỉ so sánh 2 cột như join mà phương pháp này sẽ so sánh thêm với 1 giá trị nữa
```php
 DB::table('users')
    ->join('contacts', function (JoinClause $join) {
        $join->on('users.id', '=', 'contacts.user_id')
            ->where('contacts.user_id', '>', 5);
    })
    ->get();
```

## 3.5 Subquery join
- Có thể sử dụng các phương thức joinSub, leftJoin, rightJoin để kết nối một truy vấn với sub query.
- `joinSub` nhận vào 3 tham số: sub query, alias, closure function 
```php 
$latestPosts = DB::table('posts')
    ->select('user_id', DB::raw('MAX(created_at) as last_post_created_at'))
    ->where('is_published', true)
    ->groupBy('user_id');

$users = DB::table('users')
    ->joinSub($latestPosts, 'latest_posts', function (JoinClause $join) {
        $join->on('users.id', '=', 'latest_posts.user_id');
    })->get();
```

## 3.6 Lateral Joins
- Lateral Joins hiện được hỗ trợ với PostgreSQL, Mysql >= 8.0.14 và sql server.
- Có thể sử dụng phương thức `joinLateral()` hoặc `leftJoinLateral()` để thực  hiện một sub query
- Mỗi phương thức này nhận 2 đối số: sub query và table alias.
- Điều kiện join phải được chỉ định trong `where` của sub query. Các Lateral join được đánh giá cho mỗi hàng và có thể tham chiếu đến các cột bên ngoài subquery.
- Ví dụ: 
    - Lấy ra thông tin user và 3 bài đăng gần nhất của từng user
    - Mỗi user có tối đa 3 row cho mỗi bài blog gần nhất của họ
    - Điều kiện join được chỉ định bằng `whereColumn` trong subquery, tham chiếu đến row người dùng hiện tại
```php
$latestPosts = DB::table('posts')
    ->select('id as post_id', 'title as post_title', 'created_at as post_created_at')
    ->whereColumn('user_id', 'users.id')
    ->orderBy('created_at', 'desc')
    ->limit(3);

$users = DB::table('users')
    ->joinLateral($latestPosts, 'latest_posts')
    ->get();
```

# 4. Unions
- Union là một phương thức được sử dụng trong Query Builder để kết hợp kết quả của hai hoặc nhiều truy vấn SQL lại với nhau
- đảm bảo rằng các hàng kết quả từ các truy vấn này được gộp lại thành một tập hợp duy nhất
- Kết quả của Union sẽ loại bỏ các hàng trùng lặp
- Nếu bạn muốn giữ lại các hàng trùng lặp, bạn có thể sử dụng unionAll.
- Nó thường được dùng khi bạn muốn lấy dữ liệu từ nhiều bảng hoặc từ cùng một bảng với các điều kiện khác nhau, rồi gộp chúng lại thành một tập hợp kết quả duy nhất.
```php 
$query1 = DB::table('table1')->select('column1', 'column2');
$query2 = DB::table('table2')->select('column1', 'column2');
$result = $query1->union($query2)->get();
```
```php
use Illuminate\Support\Facades\DB;

$first = DB::table('users')
    ->whereNull('first_name');

$users = DB::table('users')
    ->whereNull('last_name')
    ->union($first)
    ->get();
```
```sql
SELECT name, email FROM users WHERE first_name IS NULL
UNION
SELECT name, email FROM admins WHERE last_name IS NULL
```

# 5. Basic where


