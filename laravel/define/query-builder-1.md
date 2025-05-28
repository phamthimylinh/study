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
- Phương thức này gồm 2 tham số: câu truy vấn raw query và mảng các giá trị tham số ràng buộc (binding) cho câu truy vấn

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
- Có thể sử dụng phương thức `joinLateral()` hoặc `leftJoinLateral()` để thực hiện một sub query
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

## 5.1 Mệnh đề where

- Phương thức `where()` bao gồm 3 đối số:

* Đối số 1: là tên cột
* Đối số 2: có thể là bất kỳ toán tử nào csdl hỗ trợ
* Đối số 3: là giá trị để so sánh với giá trị cột

```php
$users = DB::table('users')
    ->where('votes', '=', 100)
    ->where('age', '>', 35)
    ->get();
```

- Thông thường nếu muốn so sánh giá trị cột = 1 giá trị nhất định thì có thể bỏ bớt 1 đối số toán tử.
- Một số ví dụ với toán tử mà được csdl cung cấp

```php
$users = DB::table('users')
    ->where('votes', '>=', 100)
    ->get();

$users = DB::table('users')
    ->where('votes', '<>', 100)
    ->get();

$users = DB::table('users')
    ->where('name', 'like', 'T%')
    ->get();
```

- Cũng có thể dùng mảng các điều kiện trong where

```php
$users = DB::table('users')->where([
    ['status', '=', '1'],
    ['subscribed', '<>', '1'],
])->get();
```

- Mysql và MariaDB tự động chuyển kiểu chuỗi thành số nguyên trong các phép so sánh chuỗi số, trong quá trình này các chuỗi không phải số được chuyển thành 0 => điều này có thể dẫn đến kết quả không mong muốn
- ví dụ

```php
// secret có giá trị là aaa
User::where('secret', 0)
// vẫn return ra kết quả
```

## 5.2 Mệnh đề whereOr

- Khi dùng `where` các mệnh đề sẽ nối với nhau bằng toán tử `and`, do đó có thể dùng `orWhere` để nối các điều kiện truy vấn bằng toán tử `or`
- Các đối số của phương thức `orWhere` giống với `where`

```php
$users = DB::table('users')
    ->where('votes', '>', 100)
    ->orWhere('name', 'John')
    ->get();
```

- Nếu cần gom nhóm điều kiện hoặc trong dấu ngoặc đơn hoặc clourse func

```php
use Illuminate\Database\Query\Builder;

$users = DB::table('users')
    ->where('votes', '>', 100)
    ->orWhere(function (Builder $query) {
        $query->where('name', 'Abigail')
            ->where('votes', '>', 50);
        })
    ->get();
```

Nó sẽ tương đương với cấu SQL dưới đây

```
select * from users where votes > 100 or (name = 'Abigail' and votes > 50)
```

## 5.3 Mệnh đề where not

- Phương thức `whereNot` và `orWhereNot` có thể được sử dụng để phủ định một câu truy vấn phủ định nhất định
- Ví dụ: lấy ra tất cả các sản phẩm đã hết hàng hoặc có số lượng nhỏ hơn 10

```php
$products = DB::table('products')
            ->whereNot(funtion (Builder $query) {
                $query->where('clearance', true)
                ->orWhere('price', '<', 10);
            })->get();
```

## 5.4 Where Any / All / None

- Đôi khi cần dùng cùng một ràng buộc cho nhiều cột, ví dụ trả về tất cả các bản ghi mà có bất kỳ cột nào trong danh sách có giá trị giống một giá trị -> dùng `whereAny`

```php
$user = BD::table('users')
        ->where('active', true)
        ->whereAny([
            'name',
            'email',
            'phone',
        ], 'like', 'Example%')
        ->get();
```
- Câu truy vấn trên sẽ có câu sql tương tự như sau
```
SELECT *
FROM users
WHERE active = true AND (
    name LIKE 'Example%' OR
    email LIKE 'Example%' OR
    phone LIKE 'Example%'
)
```
- Tương tự thế, phương thức `whereAll` để truy vấn ra các bản ghi mà trong đó tất cả các cột đều thoả mãn một giá trị
```php
$posts = DB::table('posts')
    ->where('published', true)
    ->whereAll([
        'title',
        'content',
    ], 'like', '%Laravel%')
    ->get();
```
- Câu truy vấn sql tương tự là
```
SELECT *
FROM posts
WHERE published = true AND (
    title LIKE '%Laravel%' AND
    content LIKE '%Laravel%'
)
```
- Phương thức `whereNone` dùng để trả về các bản ghi mà có 1 trong số các cột có value không khớp với value truyền vào
```php
$posts = DB::table('albums')
    ->where('published', true)
    ->whereNone([
        'title',
        'lyrics',
        'tags',
    ], 'like', '%explicit%')
    ->get();
```
- Câu sql tương ứng
```
SELECT *
FROM albums
WHERE published = true AND NOT (
    title LIKE '%explicit%' OR
    lyrics LIKE '%explicit%' OR
    tags LIKE '%explicit%'
)
```

# 6. JSON where clauses
- Laravel cũng hỗ trợ truy vấn dữ liệu json của các cột có chứa kiểu dữ liệu này (csdl)
- Hiện nay có MariaDB 10.3+, MySql 8.0+, postgreSQL 12.0+, SQL Server 2017+ và SQLite 3.39.0+
- Để truy vấn các cột dl có kiểu json dùng toán tử `->`
```php
$users = DB::table('users')
    ->where('preferences->dining->meal', 'salad')
    ->get();
```
- Cũng có thể truy vấn chứa thay vì =
```php
$users = DB::table('users')
    ->whereJsonContains('options->languages', 'en')
    ->get();
```
- Nếu dùng hệ quản trị csdl MariaDB, MySql, hoặc PostgreSQL, bạn có thể truyền vào một mảng giá trị khi dùng `whereJsonContains`
```php
$users = DB::table('users')
    ->whereJsonContains('options->languages', ['en', 'de'])
    ->get();
```
- Cũng có thể dùng phương thức `whereJsonLength` để truy vấn mảng json theo length
```php
$users = DB::table('users')
    ->whereJsonLength('options->languages', 0)
    ->get();

$users = DB::table('users')
    ->whereJsonLength('options->languages', '>', 1)
    ->get();
```

# 7. Các mệnh đề where bổ xung
## 7.1 whereLike / orWhereLike / whereNotLike / orWhereNotLike
- Phương thức `whereLike` cho phép thêm mệnh đề `LIKE` vào trong câu truy vấn
- Phương thức này cung cấp một cách không phụ thuộc vào csdl để thực hiện khớp chuỗi, và khả năng chuyển đổi phân biệt hoa thường, default là không phân biệt hoa thường
```php
 $users = DB::table('users')
    ->whereLike('name', '%John%')
    ->get();
```
- Để phân biệt hoa thường thì bật đối số `caseSensitive`
```php
$users = DB::table('users')
    ->whereLike('name', '%John%', caseSensitive: true)
    ->get();
```

- Phương thức `orWhereLike` dùng để thêm mệnh đề "or" vào trong điều truy vấn
```php
$users = DB::table('users')
    ->where('votes', '>', 100)
    ->orWhereLike('name', '%John%')
    ->get();
```

- Cũng tương tự thế phương thức `orWhereLike` cho phép thêm "NOT LIKE" vào điều kiện
```php
$users = DB::table('users')
    ->whereNotLike('name', '%John%')
    ->get();
```

- Phương thức `orWhereNotLike` thêm "or" và "NOT LIKE" vào điều kiện 
```php
 $users = DB::table('users')
    ->where('votes', '>', 100)
    ->orWhereNotLike('name', '%John%')
    ->get();
```
- Lưu ý `whereLike` với option phân biệt hoa thường đang không hỗ trợ trên hệ quản trị csdl SQL Server.

## 7.2 whereIn / whereNotIn / orWhereIn / orWhereNotIn
- Phương thức `whereIn` xác định rằng một cột có phải nằm trong một mảng giá trị nhất định hay không
```php
$users = DB::table('users')
    ->whereIn('id', [1, 2, 3])
    ->get();
```
- Phương thức `whereNotIn` xác định rằng một cột không nằm trong một mảng giá trị nhất định
```php
$users = DB::table('users')
    ->whereNotIn('id', [1, 2, 3])
    ->get();
```
- Cũng có thể dùng truy vấn vào tham số thứ hai của  `whereIn` và `whereNotIn` chứ không chỉ là một mảng value
```php
$activeUsers = DB::table('users')->select('id')->where('is_active', 1);

$users = DB::table('comments')
    ->whereIn('user_id', $activeUsers)
    ->get();
```
- Câu SQL tương tự như sau
```
select * from comments where user_id in (
    select id
    from users
    where is_active = 1
)
```
- Bên cạnh đó cũng có thể dùng `whereIntegerInRaw` hay `whereIntegerNotInRaw` trong trường hợp mảng giá trị truy vấn lớn giúp giảm bộ nhớ

## 7.3 whereBetween / orWhereBetween
- Phương thức `whereBetween` xác định các bản ghi có giá trị của 1 cột nằm giữa khoảng giá trị:
```php
$users = DB::table('users')
    ->whereBetween('votes', [1, 100])
    ->get();
```

## 7.4 whereNotBetween / orWhereNotBetween
- Phương thức `whereNotBetween` xác định rằng giá trị của 1 cột có nằm ngoài khoảng 2 giá trị không
```php
$users = DB::table('users')
    ->whereNotBetween('votes', [1, 100])
    ->get();
```

## 7.5 whereBetweenColumns / whereNotBetweenColumns / orWhereBetweenColumns / orWhereNotBetweenColumns
- Phương thức `whereBetweenColumns` xác định giá trị của 1 cột có nằm giữa giá trị của 2 cột khác trong cùng 1 bảng không
```php
$patients = DB::table('patients')
    ->whereBetweenColumns('weight', ['minimum_allowed_weight', 'maximum_allowed_weight'])
    ->get();
```
- Phương thức `whereNotBetweenColumns` xác định giá trị 1 cột có nằm ngoài giá trị của 2 cột cùng trong 1 bảng không
```php
$patients = DB::table('patients')
    ->whereNotBetweenColumns('weight', ['minimum_allowed_weight', 'maximum_allowed_weight'])
    ->get();
```

## 7.6 whereNull / whereNotNull / orWhereNull / orWhereNotNull
- Phương thức `whereNull` xác định rằng 1 cột có giá trị null hay không?
```php
$users = DB::table('users')
    ->whereNull('updated_at')
    ->get();
```
- Phương thức `whereNotNull` xác định rằng giá trị của cột không null
```php
$users = DB::table('users')
    ->whereNotNull('updated_at')
    ->get();
```

## 7.7 whereDate / whereMonth / whereDay / whereYear / whereTime
- Phương thức `whereDate` có thể được dùng để so sánh giá trị của 1 cột với 1 ngày
```php
$users = DB::table('users')
    ->whereDate('created_at', '2016-12-31')
    ->get();
```
- Phương thức `whereMonth` phương thức này có thể được sử dụng để sử dụng để so dánh giá trị của một cột với 1 tháng cụ thể
```php
$users = DB::table('users')
    ->whereMonth('created_at', '12')
    ->get();
```
- Phương thức `whereDay` dùng để so sánh giá trị của một cột với một ngày cụ thể trong tháng
```php
$users = DB::table('users')
    ->whereDay('created_at', '31')
    ->get();
```
- Phương thức `whereYear` dùng để so sánh giá trị của 1 cột với 1 năm cụ thể
```php
$users = DB::table('users')
    ->whereYear('created_at', '2016')
    ->get();
```
- Phương thức `whereTime` dùng để so sánh giá trị cột với 1 thời gian cụ thể
```php
$users = DB::table('users')
    ->whereTime('created_at', '=', '11:20:45')
    ->get();
```

## 7.8 wherePast / whereFuture / whereToday / whereBeforeToday / whereAfterToday
- Phương thức `wherePass` và `whereFuture` có thể dùng để xác định xem giá trị của một cột là ở quá khứ hay ở tương lai
```php
$invoices = DB::table('invoices')
    ->wherePast('due_at')
    ->get();

$invoices = DB::table('invoices')
    ->whereFuture('due_at')
    ->get();
```
- Phương thức `whereNowOrPass` và `whereNowOrFuture` để có thể sử dụng xác định xem giá trị của một cột là ở quá khứ hay tương lai, bao gồm cả ngày và giờ hiện tại
```php
$invoices = DB::table('invoices')
    ->whereNowOrPast('due_at')
    ->get();

$invoices = DB::table('invoices')
    ->whereNowOrFuture('due_at')
    ->get();
```
- Phương thức `whereToday` và `whereAfterToday` xác định 1 cột có giá trị là ngày hôm nay, trước hay sau ngày hôm nay
```php
$invoices = DB::table('invoices')
    ->whereToday('due_at')
    ->get();

$invoices = DB::table('invoices')
    ->whereBeforeToday('due_at')
    ->get();

$invoices = DB::table('invoices')
    ->whereAfterToday('due_at')
    ->get();
```
## 7.9 whereColumn / orWhereColumn
- Phương thức `whereCoumn` có thể được sử dụng để xác định rằng 2 cột có bằng nhau hay không
```php
$users = DB::table('users')
    ->whereColumn('first_name', 'last_name')
    ->get();
```
- Và cũng có thể truyền vào toán tử so sánh
```php
$users = DB::table('users')
    ->whereColumn('updated_at', '>', 'created_at')
    ->get();
```
- Có thể truyền vào 1 mảng các cột trong phương thức `whereColumn`, các điều kiện này sẽ được nối với nhau bằng toán tử and
```php
$users = DB::table('users')
    ->whereColumn([
        ['first_name', '=', 'last_name'],
        ['updated_at', '>', 'created_at'],
    ])->get();
```
